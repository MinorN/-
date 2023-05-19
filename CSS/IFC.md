# IFC

什么叫做`IFC`？`IFC`：`inline formatting content`，是不是很眼熟？想想`BFC`

## font-size

```html
<span>你好，HELLO</span>

span{
	font-size:100px;
}
```

请问这`100px`是什么？

A. H的上下高度； B. 你的上下高度

**实际上都不是**。那么用`font-size`是在规定什么呢？

想要知道，测试一下下面这段代码：(前提是内部有如下字体)

```html
<p>
    <span>你A</span>
	<span>你B</span>
	<span>你C</span>
</p>

span{
	font-size:100px;
	border:1px solid red;
}
span:nth-child(1){
	font-family:STSong;
}
span:nth-child(2){
	font-family:Langtinghei SC;
}
span:nth-child(3){
	font-family:Xingkai SC;
}
```

你会发现，每个`span`都不一样。但是你会发现，每个span底边在一条线上，这是由于默认`baseline`对齐（但是可能会有例外，所以很麻烦）。

一个字体会定义一个`em-square`，可以理解为一个方块上面刻着字，这个方块会被设定为宽高均为1000相对单位（也可以自己设置），`font-size`指的是这个方块的大小。字体度量都是以这个方块设置的。写`font-size`会按照这个方块（1000）等比缩小、放大。每个字体在这个方块内占得比例不一样，所以会呈现出不同的效果。

## line-height

同时，你还可以发现每个`span`的边框大小也不一样。这是因为字体设计师自己设置的`line-height`

你要是自己设置`line-height`没有变化。

那么给出`line-height`有什么意义吗？

现在来介绍一下**行盒**

```css
// 加入以下CSS
p{
    border:1px solid black;
    margin:20px 0;
}
```

这就是**行盒**

现在如果你修改`line-height:100px`，这时候就会实现自动居中。

问：当两个元素字体不一样，为什么会导致盒子变高？

是因为字体可能基线高度不一样，在不同字体对齐时候，会依照基线来对齐，就会导致行盒变高。

如果没有设置`line-height`，`line-height`是由字体设计师的推荐行高。强行指定可能会导致一行容纳不下一个字的情况（有可能），所以一般`line-height:1.5`(常用字体，别稀奇古怪)。

## vertical-align

```css
<p>
    <span>Ax</span>
    <span>xA</span>
</p>
span{
	font-size:100px;
	border:1px solid red;
}
span:nth-child(1){
	font-family:Hack;
}
现在能看出来是按照基线对齐的
span:nth-child(1){
    vertical-align:top;
}
现在是用什么来对齐的？
按照实际占用面积来对齐。
可以先保留一个span来看实际占用面积
```

## 图片下面为什么有空隙

```html
<div>
    <img src="#"/>
</div>
```

问：`div`多高？

```css
div{
    border:1px solid red;
}
```

因为`img`为`inline-block`元素，两个内联元素依靠`baseline`对齐，那么一个内联元素依靠什么对齐？

```css
div{
    font-size:100px
}
```

什么？下面空隙怎么变大了？

```html
<div>
    x<img src="#"/>
</div>
```

嗯？原来是跟着这个`x`对齐，你告诉我没有？`CSS`就是这样，它会跟默认的基线对齐。

会有人用`font-size:0;`，千万不要这样写，万一会引起其他`BUG`呢？

所以怎么解决图片下面空隙问题？

```css
img{
    vertical-align:top/buttom/middle;
    只要不是默认值就行
}
或者
img{diplay:block}
```

## 小提示

尽量不要用`inline-block`做布局，会出现使用`vertical-align`对不齐的情况。

所以为什么不用`flex`或者`float`呢？



