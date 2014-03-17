---
title: '从box-module到box-sizing'
layout: post
guid: urn:uuid:3F7915A9-96FB-C962-E639-A66FAB192A83
tags:
    - css
---
什么是box-sizing?一步一步来，循序渐进 :)

box-sizing是css3的box属性。

####什么是box?

> 页面的构成元素，包括 SPAN DIV TABLE INPUT IMG 等等。仔细观察每个元素，它们的形状都是矩形的，严格的说，元素形成了一个矩形的区域，这个区域就是 CSS 中最基本的布局要素， 常被称作 "box"。

####说到box，常常被一起提到的还有box-module，它又是什么？

> 为了给文档树中的各个元素排版定位（布局），浏览器会根据渲染模型为每个元素生成四个嵌套的矩形框， 分别称作 content box、padding box、border box 和 margin box，它们是不可分割的，并可能会重合， 这就是 CSS 规范中描述的“框模型”（box model）。它是以 CSS 的角度去看一个元素被渲染后的抽象形态。 是一个元素自身的构成部分，不同于布局：多个元素在页面上的定位。

####box-module不止一种？

一种是W3C的标准模型，另一种是IE的传统模型(IE6以下，不含IE6版本或“QuirksMode下IE5.5+”)。

####这两种有什么区别？

这两种模型都是计算元素计算的尺寸，他们的主要区别是在计算尺寸的方式上有所不同。看个例子一目了然。

    .box-test{
		width:200px;
		height:200px;
		padding:20px;
		border:1px solid #777;
		background:#000;
	}

很明显可以看出，w3c标准模式下的元素的内容宽高（content width和content height）等于IE传统模式下元素内容+padding+border尺寸的总和。可以用下面的公式来表述这一关系:

1、W3C的标准模式box-module:
	
	/*外盒尺寸计算（元素空间尺寸）*/
	Element空间高度 = content height + padding + border + margin
	Element 空间宽度 = content width + padding + border + margin
	/*内盒尺寸计算（元素大小）*/
	Element Height = content height + padding + border （Height为内容高度）
	Element Width = content width + padding + border （Width为内容宽度）
 

2、IE传统模式下box-module（IE6以下，不含IE6版本或“QuirksMode下IE5.5+”）:
	
	/*外盒尺寸计算（元素空间尺寸）*/
	Element空间高度 = content Height + margin (Height包含了元素内容宽度，边框宽度，内距宽度)
	Element空间宽度 = content Width + margin (Width包含了元素内容宽度、边框宽度、内距宽度)
	/*内盒尺寸计算（元素大小）*/
	Element Height = content Height(Height包含了元素内容宽度，边框宽度，内距宽度)
	Element Width = content Width(Width包含了元素内容宽度、边框宽度、内距宽度)

明确了这两种box-module，再来看box-sizing。

    box-sizing:content-box||border-box||inherit

开头的时候说过box-sizing是box的属性，它的取值影响的是box-module的模式。`box-sizing:content-box`,浏览器会按照W3C标准模式计算元素的尺寸，`box-sizing:border-box`，会按照IE传统模式计算。

比如上面的例子，我们在`.box-test`中添加一条`box-sizing:border-box`，那么在符合w3c标准的浏览器中，`div.box-test`的宽度会像IE传统模式那样会被计算为**内容宽度**+**内边距**+**边框宽度**=200px。

###box-sizing在实际中有哪些应用场景？

实际项目中用到box-sizing一个重要的地方就是统一form表单元素在各个浏览器的表现。有些表单元素例如`input[type="submit"]、input[type="reset"]、button、select`等等还是支持IE传统下box-module标准，而其它如`input[type=text],textarea`则遵循w3c标准。并且表单元素在各个浏览器中尺寸都有差别。如果想要统一他们的尺寸，可以采用将他们的box-sizing设置为border-box,这样在设置他们的宽高时，都会得到相同的元素尺寸。IE6,7不支持这个属性，使用hack即可。

box-sizing还可以应用在调整元素布局上，尤其是左右两列固定宽度布局，当左右两侧的宽度总和等于父元素宽度时，如果需要在两列上添加padding，border，就会造成撑出错位，这时可使用`box-sizing:border-box`，在添加padding或border后，元素的内盒宽度不会改变，避免上述问题。