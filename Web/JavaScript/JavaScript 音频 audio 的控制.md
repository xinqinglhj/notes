有时我们需要使用js来控制播放器实现音乐的播放，暂停。或者使用 js 播放一些音效。

### HTML 代码
下面是基本的 HTML 片段：

```html
<audio>
    <source = src="hangge.mp3" type="audio/mp3">
    <source = src="hangge.ogg" type="audio/ogg">
    您的浏览器不支持音频的播放。
</audio>
```

audio 元素有多个控制属性，如`autoplay`、`control`等。

### 暂停和播放
控制音频的播放，最基本的方法是`play()`和`pause()`两个方法：

```javascript
var audio = document.querySelector('audio');

// 音频播放
audio.play();

// 音频暂停
audio.pause();
```

### 控制播放进度
如果要控制音频的播放进度，或者循环播放等，则可以通过重置音频元素的`currentTime`属性的值来实现。

`currentTime`属性表示音频当前播放的进度，单位是秒(s)。

```javascript
var audio = document.querySelector('audio');

# 从头播放
audio.currentTime = 0;
audio.play();
```

### 动态创建 audio 元素

```javascript
# 方式一
var audio = document.createElement('audio');
audio.src = 'hangge.mp3';
audio.play();

// 方式二
var audio = new Audio("hangge.mp3");
audio.play();
```

在创建的时候，可以通过`canPlayType()`方法可以判断浏览器支持的编码方式，从而设置对应的音频文件：

```javascript
if (audio.canPlayType("audio/mp3")) {
    audio.src = "hangge.mp3";
}else if(audio.canPlayType("audio/ogg")) {
    audio.src = "hangge.ogg";
}
```

### 注意点
在某些移动端浏览器中，如果创建的音频元素，一开始不设置为自动播放，那么很有可能之后就无法使用`play()`来播放音频了。这种情况下，就需要设置音频的`autoplay`属性为 true，然后借助`currentTime`属性和`pause()`方法来变通的解决：

```javascript
var audio = document.createElement('audio');
audio.controls = false;  // 不显示控制器
audio.muted    = true;   // 静音
audio.autoplay = true;   // 自动播放
audio.src = 'hangge.mp3';

document.body.appendChild(audio);
audio.play();
```

