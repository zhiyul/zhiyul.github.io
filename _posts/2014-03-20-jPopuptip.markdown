---
title: '自适应位置tipbox原生js插件-jPopupbox'
layout: post
guid: urn:uuid:4dc0432e-47d2-dc3d-1189-99ef56fc6b31
tags:
    - javascript
---
前段时间做公司里一个电商类网站，需要用到一个tips功能，在鼠标点击会移到目标元素上的时候，在元素附近弹出一个提示框，显示更多详细信息。当时使用jquery来做的。这两天有点闲时间把这个功能用js重写了一边，封装成插件。一方面用到tips的地方还是比较多的，有些不需要用的jquery的地方，没要为了这个功能再引入jquery；另一方便也是多练练写js，平时用jquery比较多，js有些生疏。

名字叫做jPopuptip(感觉这个名字有点拙计 = =)

[demo](http://zhiyul.github.io/jPopuptip)

[github](https://github.com/zhiyul/jPopuptip)

####特点

- 原生js，不依赖任何库
- 兼容IE6+
- 自适应位置，保持tip窗口始终全部显示在当前页面视口
- 支持hover，click两种触发模式，可统一设置也可单独设置
- 自定义tips窗口尺寸，样式，与目标元素的距离等
- 提示框默认出现顺序优先级：右上 -> 左上 -> 右下 -> 左下

####使用说明

##init

需要引入jPopuptip.js和jPopuptip.css文件

	<link rel="stylesheet" type="text/css" href="jPopuptip.css">
	<script type="text/javascript" src="jPopuptip.js"></script>

	<a class="btn" data-popuptip="show" data-content="这是个按钮!">more</a>
    <a class="btn" data-popuptip="show" data-triggle="click" data-content="你看到我了！">
		点击就能看到我
	</a>

为需要弹出框的元素添加 data-popuptip="show"

通过设置 data-triggle="click" 可为元素添加触独立触发方式

通过设置 data-content="hello",为弹出框添加内容

    var p = new jPopuptip({
		width : 'auto',		//弹出框宽度，默认为auto
		height : 'auto',	//弹出框高度，默认为auto
        'boxOffsetX' : 20,	//弹出框偏移目标呢元素左/右边界距离，默认为20px
        'boxOffsetY' : 5,	//弹出框偏移目标呢元素上/下边界距离，默认为20px
        'arrowSize' : 10,	//弹出框偏箭头大小，默认10px
        'triggle' : 'hover'	//触发弹出框事件，默认为hover，可设置为click
	});

在编写过程中遇到一些问题或者反复修改的地方，下篇日志来总结下。