### 状态切换
可以使用以下命令使文件在三种状态间切换：

![切换状态](http://7xkt52.com1.z0.glb.clouddn.com/markdown/1464932592834.png)

也可以跳过中间状态切换：

![跳过中间状态的切换](http://7xkt52.com1.z0.glb.clouddn.com/markdown/1464932635964.png")

### 追加 Commit
如果想对刚才做的 commit 做适当修改，可以紧接着写修改的操作，然后执行命令`git commit --amend`即可将暂存区中的内容补充到最近的一次 commit 中。

如果刚才提交完没有作任何改动，直接运行此命令的话，相当于有机会重新编辑提交说明，但将要提交的文件快照和之前的一样。

如果刚才提交时忘了暂存某些修改，可以先补上暂存操作，然后再运行`--amend`提交：

```git
# 做一次提交
git commit -m 'initial commit' 

# 将漏掉的文件添加到暂存区
git add forgotten_file
# 将这个漏掉的文件追加到刚才的提交中
git commit --amend
```
上面的这三条命令最终只是产生一个提交，第二个提交命令修正了第一个的提交内容。

### 放弃本地修改，强制更新

```git
# 仅仅拉取远程仓库中的所有内容
git fetch --all

# 不做任何的合并 git reset 把HEAD指向刚刚下载的最新的版本
git reset --hard origin/master
```

### 删除文件
如果只是简单地从工作目录中手工删除文件，运行`git status`时就会有`Changes not staged for commit`的提示。

要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除，然后提交。可以用以下命令完成此项工作：

`git rm <file>`

如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项`-f`：

`git rm -f <file>`

如果把文件从暂存区域移除，但仍然希望保留在当前工作目录中，换句话说，仅是从跟踪清单中删除，使用`--cached`选项然后将文件添加到 .gitignore 文件中即可：

`git rm --cached <file>`


### 推送本地分支到远程
如果在本地新建了一个分支，然后推送到远程，可以使用如下的方式：

`git push origin local_branch:remote_branch`

这个操作中，`local_branch`必须为你本地存在的分支，`remote_branch`为远程分支，如果`remote_branch`不存在则会自动创建分支。

### 删除远程分支
与推送本地分支到远程类似，只需要在命令中将本地分支名称留空，即可删除远程分支。

`git push origin :remote_branch`

在这里，`local_branch`留空，则是删除远程`remote_branch`分支。

### 撤销 git add
如果需要撤销添加到暂存区中的文件，可以借助 reset 命令：

`git reset <文件名>`

如果想从暂存区移除所有没有提交的修改，就不需要使用文件名参数。

之所以可以这样，是因为 reset 命令会将文件恢复到指定版本，同时其参数`--hard | --mixed | --soft`可以用来恢复效果影响的程度。默认情况(不带参数的时候)，就是 --mixed 效果，也就是会将指定版本的文件同步到当前分支的 HEAD 和暂存区中。

### 从当前分支移除未追踪的本地文件
假设你凑巧有一些未被追踪的文件（因为不再需要它们），不想每次使用 git status 命令时让它们显示出来。下面是解决这个问题的一些方法：

```shell
git clean -f -n         # 1
git clean -f            # 2
git clean -fd           # 3
git clean -fX           # 4
git clean -fx           # 5
```

* (1): 选项 -n 将显示执行（2）时将会移除哪些文件。-n option will let you know what files will be removed if you run (2).
* (2): 该命令会移除所有命令（1）中显示的文件。This will remove all files as reported by command-(1).
* (3): 如果你还想移除文件件，请使用选项 -d。-d if you also want to remove directories.
* (4): 如果你只想移除已被忽略的文件，请使用选项- X。-X if you just want to remove ignored files.
* (5): 如果你想移除已被忽略和未被忽略的文件，请使用选项 -x。 if you want to remove both ignored and non-ignored files

### 美化 diff
当你要暂存或 commit 之前，看看你修改了哪些内容是个好习惯，执行`git diff`命令，默认的输出格式比较难懂，我们可以美化一下，在`~/.gitconfig`中添加如下 alias：

```conf
[alias]
  d = "!f() { [ -z \"$GIT_PREFIX\" ] || cd \"$GIT_PREFIX\" && git diff --color \"$@\" | diff-so-fancy  | less --tabs=4 -    RFX; }; f"
```

然后执行`git d`替代`git diff`，结果会清晰许多。

### git pull --rebase 拉取远程更新时避免过多的 commit log
我们分别 checkout –b 出来两个分支，独立开发互不干扰。在 develop_newfeature_authorcheck 里修改了点东西，push 到 develop。然后 checkout 到 develop_newfeature_apiwrapper，执行`git pull`，将 develop_newfeature_authorcheck 分支的修改直接拉下来与本地代码 merge，且产生一个 commit，也就是 merge commit。

![](http://7xkt52.com1.z0.glb.clouddn.com/markdown/1479049400931.png)

此处的 F commmit 是无意义的，它只是一个 merge commit。而且个 commit message 里面的 branch 日后也不存了，这些分支都会被清除掉，所以完全没有必要生成和保留这个 merge commit。

如果使用`git pull –rebase`这样的结局就完全不一样：它并不会产生一个 merge commit，而是会将你的 E commit 附加到 D commit 的结尾处。也就是说，会将你的 develop_newfeature_apiwrapper 分支的基点变成在 develop_newfeature_authorcheck 分支上的 D commit。

![](http://7xkt52.com1.z0.glb.clouddn.com/markdown/1479049586790.png)

### 删除本地中存在的、已经在远程被删除的分支
可以通过命令`git remote show origin`来查看有关于 origin 的一些信息，包括分支是否 tracking。当我们删除一个远程的分支之后，在本地使用这个命令会显示出类似如下的信息：

![](http://7xkt52.com1.z0.glb.clouddn.com/markdown/1479049827419.png)

本地删除的分支那一行上提示你可以通过`git remote prune`移除这个分支。（也就是说你可以刷新本地仓库与远程仓库的保持这些改动的同步。）


```git
git remote prune origin
```

执行之后，这个在远程删除的分支在你本地仓库也将被删除。再通过`git branch –a`来查看就可以看到其已经被删除了：

![](http://7xkt52.com1.z0.glb.clouddn.com/markdown/1479049942671.png)

### 从 Git 仓库中永久删除文件或目录
我们常用的`git rm`仅对`Working Tree`构成影响，如果想永久的删除仓库中的文件或目录，那么就要用到`git filter-branch`命令了。`git filter-branch`会检索整个 Commit 历史，逐一改写 Commit Object，重构整个 Tree。

```shell
git filter-branch --tree-filter 'rm -rf path/folder' HEAD
git filter-branch --tree-filter 'rm -f path/file' HEAD
```

也可以指定检索的 Commit 历史的范围：

```shell
git filter-branch --tree-filter 'rm -rf path/folder' 347ae59..HEAD
```

最后，不要忘了向仓库强制推送所有的变化：

```shell
git push origin master --force
```

执行`git filter-branch`命令后，已经标记为删除的 Object 在本地仓库中要到过期后才会解除关联和进行垃圾回收。如果想立即解除关联，执行垃圾回收，可以这么做：

```shell
# 先检查哪些 tags 和 branch 引用了这些 Object，并根据结果更新引用
git for-each-ref --format='delete %(refname)' refs/original | git update-ref --stdin
# 然后使这些引用立即过期，并立即执行垃圾回收
git reflog expire --expire=now --all
git gc --prune=now
# 之后就可以推送到远程仓库了
git push origin --force --all
git push origin --force --tags
```

> 不到迫不得已，不要轻易使用`git filter-branch`，因为它重构了整个 Tree，所以每个开发人员都需要重新克隆仓库到本地，对于有很多开发者参与的大型项目来说，这么做会给很多人带来麻烦，与其事后对仓库内容进行修正，不如伊始就对每个 Commit 慎之又慎。

转摘：[从 Git 仓库中永久删除文件或目录](http://www.jmlog.com/permanently-remove-files-and-folders-from-git-repository/)

### 给 Git 仓库瘦身
由于 Git 自身的原理特性，每一次提交的改变都会以新文件的形式存储在本地项目根目录下的`.git`中，会在`.git/objects`下面形成一个 Blob（一段二进制数据）文件记录。这意味着，即使你只改动了某个文件的一行内容，Git 也会生成一个全新的对象来存储新的文件内容。所以 Git 仓库随着时间变化会自增长，我们往往忽视了这种潜在的危险。

有两种方法能够进行瘦身：

**方法一** 压缩 Git 仓库

这种方法治标不治本，但是使用比较简单，执行这条命令就可以了：`git gc --prune=now`。

执行这条命令后，当你再次使用`du -hs`的时候会发现仓库大小有一定的变小。其实 Git 自身在可承受范围内会自动用 gc 帮你压缩打包，所以除非真的遇到 pull、push 都困难的时候，可以不用手动执行。

这个方法明显的缺点在于压缩的效果有限，且大文件还一直在之后的每次提交中，为以后埋下隐患。

**方法二** 删除 Git 提交记录中大文件，再 gc 压缩

使用这种方式稍微麻烦点，首先要查找大文件，命令如下：

```shell
git rev-list --objects --all | grep "$(git verify-pack -v .git/objects/pack/*.idx | sort -k 3 -n | tail -5 | awk '{print$1}')"
```

比如最终找到了`nspatientList1.txt`文件比较大，需要删除：

```shell
git filter-branch --force --index-filter 'git rm -rf --cached --ignore-unmatch bin/nspatientList1.txt' --prune-empty --tag-name-filter cat -- --s
```

删除之后会有大量输出，显示已经从各自历史 log 中剔除掉关于这个大文件的信息，之后可以使用 gc 命令再次压缩：

```shell
git gc --prune= now
```

然后再进行提交即可。

