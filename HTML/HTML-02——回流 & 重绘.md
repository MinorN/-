# HTML-02——回流 & 重绘

首先，什么是回流？什么是重绘？需要注意的是，回流和重绘都是在页面已经生成之后进行的操作

* 回流：当我们渲染树的一部分，由于元素的一些尺寸、大小、隐藏显示等，页面需要重新布局，这样的操作是回流
* 重绘：重新绘制，比如说一些颜色、风格等发生了改变，但是我页面的布局不需要发生变化，这样的操作叫重绘

举几个例子：

* 回流：
  1. 改变了一个div的大小
  2. 页面中一个元素消失不见
* 重绘
  1. 改变了字体颜色
  2. 改变背景颜色

需要注意的是：**回流一定也会发生重绘，重绘不一定发生回流**

由于回流会引发一系列DOM节点的变化，所以一般来说，回流的性能开销是要比重绘高的，所以说，我们需要尽可能避免回流

那么问题来了：如何有效的避免回流？

1. 手动修改DOM的样式的时候，不要一条一条修改，直接预先定义好需要添加样式的class，然后直接修改DOM的`className`即可

```js
const el = document.getElementById('name')
for(let i=0;i<10;i++) {
    el.style.top  = el.offsetTop  + 10 + "px";
    el.style.left = el.offsetLeft + 10 + "px";
}

el.width = '200px'
el.height = '200px'
el.border = '1px solid red'
```

这样子每次都会取一次`offsetTop`和`offsetLeft`，那么循环多少次就会取多少次，可以先存起来；并且，我们直接修改了样式，这样也会影响性能

```js
const el = document.getElementById('name')
let offLeft = el.offsetLeft, 
let offTop = el.offsetTop
for(let i=0;i<10;i++) {
  offLeft += 10
  offTop  += 10
}

el.style.left = offLeft + "px"
el.style.top = offTop  + "px"

el.classList.add('addStyle')

// css
.addStyle{
 	width: 200px
	height: 200px
	border: 1px solid red
}
```



2. 合理使用transform