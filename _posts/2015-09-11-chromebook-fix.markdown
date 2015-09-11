---
title: ‘chromebook内置应用适用性'
layout: post
guid: urn:uuid:bead79e3-3f6f-453e-951d-314c29fbc325
tags:
    - tips
---

最近公司里一款叫做AirDroid的产品想和三星合作内置在新版的chrombook中，但是google提高了对内置应用的要求，在检测AirDroid之后提出了一些修改意见:

> Accessibility Requirements:
> 
> - All functions must be able to be reached via the keyboard without using a mouse
> - All controls and images must have meaningful labels
> - Focus must behave properly within the app 
> - Users must be able to understand all information without relying solely on color or sound

大致意思是：

- 所有的操作按钮都能被tab到(通过按tab键切换焦点，焦点能切换到目标元素上)，并且通过键盘enter、shift + enter 能够触发相应的行为
- 所有的控制按钮、图标、图片都必须有相应的说明文字
- 焦点切换必须限制在当前的app内
- 用户能够不完全依赖颜色或者声音来理解信息

不难发现，google对内置应用的额外要求主要是为了满足只用键盘和有视力缺陷的用户的使用需求。由于AirDroid前期没有考虑过这类用户，因此应用中存在着大量无法使用键盘进行的操作和无法提供给屏幕阅读软件有效信息的按钮、图标等。