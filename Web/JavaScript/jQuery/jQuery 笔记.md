## DOM 操作
### 删除元素
`remove()` - 删除被选元素（及其子元素）
`empty()`  - 从被选元素中删除子元素

### 添加元素
`before()` - 在元素前面添加元素
`after()`  - 在元素后面添加元素


## 获取标签名(tagName)
如果是为了取到 tagName 后再进行判断，那直接用下面的代码会更方便：
`$(element).is('input')`。

如果是要取到标签用作到别的地方，可以使用一下代码：`$(element)[0].tagName` 或：`$(element).get(0).tagName`，或者：`$(element).prop("tagName")`。

## 点击 text 的 input 后，直接选中其中的所有文字:

```js
$("input:text").click(function(){
    $(this).select();
});
```

## 获取 select 标签的文本和值

```js
jQuery("#select1  option:selected").text(); // 获取选中的值的文本
jQuery("#select1").val(); // 获取选中的值的 value 属性值
```

## 滚动到指定位置


```js
var container = $('div'),
    scrollTo  = $('#row_8');

container.scrollTop(
    scrollTo.offset().top - container.offset().top + container.scrollTop()
);
```


