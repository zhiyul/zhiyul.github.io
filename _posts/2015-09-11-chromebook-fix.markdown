---
title: chromebook内置应用适用性
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

- 所有的功能都能通过只使用键盘来操作
- 所有的控制按钮、图标、图片都必须有相应的说明文字
- 焦点切换必须限制在当前的app内
- 用户能够不完全依赖颜色或者声音来理解信息

不难发现，google对内置应用的额外要求主要是为了满足只用键盘和有视力缺陷的用户的使用需求。由于AirDroid前期没有考虑过这类用户，因此应用中存在着大量无法使用键盘进行的操作和无法提供给屏幕阅读软件有效信息的按钮、图标等。

知道了问题所在，就可以针对性的来修复了。

首先，要实现只用键盘就能进行操作，那么所有的操作按钮、图标等都要能够获取焦点，即通过按tab键能够"到达”目标元素。要满足这一点，我们先搞清楚什么样的元素能够获取焦点—关于这一点w3c有很详细的规定：[w3c-about foucs](http://www.w3.org/TR/html5/editing.html#focus)，明白了这些之后，就可以把希望获取焦点的元素改成 **focusable element** 。有几点在改的时候比较有体会：

- a 元素一定要带上href属性，除非你不想他被tab到。如果想禁止它的默认行为就使用`<a href="javascript:;"></a>`
- 按钮最好使用button元素，尽量不要用div或者其他元素来模拟
- `display:none` 和 `visibility: hidden` 的元素都不能获取到焦点

其实只要注意标签语义化，能够避免很多需要增加tabindex属性来获取焦点的地方。

在上面的基础上，为了让我们的应用可以被无障碍使用，需要符合WAI-ARIA（无障碍网页应用）的规范。

> 	WAI-ARIA指无障碍网页应用。主要针对的是视觉缺陷，失聪，行动不便的残疾人以及假装残疾的测试人员。尤其像盲人，眼睛看不到，其浏览网页则需要借助辅助设备，如**屏幕阅读器**，屏幕阅读机可以大声朗读或者输出盲文。
> 
> 而ARIA就是可以让**屏幕阅读器**准确识别网页中的内容，变化，状态的技术规范，可以让盲人这类用户也能无障碍阅读！

关于WAI-ARIA的详细属性，这里有一篇详细的介绍 [WAI-ARIA无障碍网页应用属性完全展示](http://www.zhangxinxu.com/wordpress/2012/03/wai-aria-%E6%97%A0%E9%9A%9C%E7%A2%8D%E9%98%85%E8%AF%BB/) 。我在修改中主要使用几个常用的属性：

- role： 使用role属性告诉辅助设备（如屏幕阅读器）这个元素所扮演的角色，使之语义化，方便浏览器对其具体功能进行识别
- aria-label： 标记当前的元素，屏幕阅读软件会优先阅读元素上的这个属性。你想让用户知道这个元素是干什么的，就在这个属性里说明。
- aria-hidden：表示元素对屏幕阅读器来说是否可见。比方这样一个元素，![icon](http://7xls2e.com1.z0.glb.clouddn.com/weatherstar-post-fix-chromebook-1) 让他获取焦点的时候，如果只想屏幕阅读器读出”messages"，那么就在img标签上设置”aria-hidden:true”，否则屏幕阅读器会把图片的文件名也读出来。

其次，焦点切换的范围必须限制在当前操作的独立窗口内（元素内）。设想这样一个场景，盲人用户当前正在登录网站，有一个登录弹出框，焦点在密码输入框上，用户知道他在登录。如果他多按了几下tab，焦点跳出了当前登录框，那么对于盲人用户来说就说产生很大的迷惑，他不知道自己现在在哪个位置，刚才登陆成功了吗？所以在他登录完成之前，需要把焦点限制在登录框内。

![login](http://7xls2e.com1.z0.glb.clouddn.com/weatherstar-post-fix-chrome-2.png)

实现这个功能需要依靠javascript，思路是：在每个独立的元素上绑定keypress事件，当用户按下tab键时，获取目标元素内所有可以获取焦点的元素，如果当前焦点元素是所有可获取焦点元素中的最后一个，那么就把焦点切换到第一个可获取到焦点的元素上。同时按下tab+shift时，反向判定。代码如下：

``` javascript
//将Tab按键切换焦点限制在某个DOM元素内
Airdroid.Util.limitTabFocus = function (event, dom, appId) {
    //如果是enter，触发元素的click事件
    if(event.keyCode === Airdroid.Config.keyCode.ENTER){
        Airdroid.Util.cancelEventBubble(event);
        //如果是文件选择框 就不出发click 不然会出现两次文件选择
        if(event.target.type != "file"){
            $(event.target).trigger('click');
        }
        return;
    }
    //默认显示在桌面的两个模块不限制tab范围
    if(event.keyCode !== Airdroid.Config.keyCode.TAB || event.isDefaultPrevented()){
        return;
    }
    Airdroid.Util.cancelEventBubble(event);
    var tabbables = dom.find(":tabbable"),
        first = tabbables.filter(":first"),
        last = tabbables.filter(":last");
    if((event.target === last[0] || event.target === dom[0]) && !event.shiftKey){
        first.trigger("focus");
        event.preventDefault();
    }else if ( ( event.target === first[ 0 ] || event.target === dom[ 0 ] ) && event.shiftKey ) {
        last.trigger( "focus" );
        event.preventDefault();
    }
};
```

其中`dom.find(":tabbable")`是jQuery UI中的方法，作用是获取元素中所有可被tab到的元素。

再次，当在应用内进行了一些操作的时候，焦点位置会发生变化，这时候需要使用javascript对焦点的位置进行一些掌控。比如，当焦点在select下拉框的某个选项上，此时点击enter选中这个选项后，下拉框隐藏，焦点会丢失，需要用代码把焦点控制在目标元素上（比如让select框重新获取焦点）。在前期开发中就应该多考虑这一点，减小后期修复工作量。

最后：一定要注意语义化！一定要注意语义化！一定要注意语义化！