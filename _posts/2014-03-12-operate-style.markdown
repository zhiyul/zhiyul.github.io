---
title: 'javascript操作样式'
layout: post
guid: urn:uuid:4c006d67-6f29-7411-ba4b-159f5a819089
tags:
    - javascript
---
原生javascript访问和操作样式是一件麻烦的事情，慢慢道来。

在html中定义样式有三种方式：

 1. 在head中通过`<link>`标签引入外部样式表
 2. 在head中通过`<style>`定义嵌入式样式
 3. 通过style特性定义针对特定元素的样式

这三种方式定义的样式优先级由低到高，后一种方式定义的样式总会覆盖前一种方式中定义的相同的样式。

###DOM样式

支持style特性的html元素在js中都有一个style属性，包含了通过html的style特性指定的样式，可以通过`element.style`方便地访问到这些样式，但是这其中不包含外部样式表或者内嵌样式表经层叠而来的样式。在js中使用短划线的css属性名都需要转化为驼峰大小写形式才能通过js来访问。比如`background-color`在js中使用要写为`backgroundColor`，没有采用短划线的则保持不变。几乎所有的css属性转换都遵循这样的规则，但有一个例外：`float`。float是js中的保留字，需要将它转换为`cssFlaot`，IE中支持的是`styleFlaot`。

更改一个元素的特定style样式，只要获得这个元素的有效引用，然后就可以通过`element.style.width'`获取元素样式，设置元素样式也是通过`element.style.width'=200px`。需要注意所有的度量值都要加上单位，在浏览器标准模式下，度量值没有单位的设置将被忽略。

style对象还有另外的一些属性和方法,其中的`cssText`用于获取style特性中的css代码，也可用来批量设置css属性 `ele.style.cssText="width:100px;height:200px;background-color:red;"`这种方式会重写整个style特性的值。

###计算的样式

style特性虽然方便但是无法访问元素从其他样式表层叠而来的样式，而不巧的是大多时候我们的样式都是写在外部样式表中的。这是就需要通过`getComputedStyle()`，这个方法由`document.defaultView`提供。getComputedStyle接受两个参数，第一个是你想要获取样式的元素，第二个是一个伪类（：after），不需要伪类可以传null，像这样：`var eleStyle = document.defaultView.getComputedStyle('ele',null);`，返回的eleStyle是一个类似style的对象，里面包含了ele计算后的样式。IE不支持getComputedStyle,但IE中具有style的元素还有另外一个属性`currentStyle`,包含当前元素全部计算后的样式，来看个具体的例子

    
	<style>
	    .myDiv{
	        width:100px;
	        height:100px;
	        background-color:red;
	    }
	</style>
	<div class="myDiv" style="background-color:blue;"></div>
	<script>
	    var div = document.getElementsByTagName[0];
	    var divStyle = getEleStyle(div);
	    console.log(divStyle.width);              //"100px"
	    console.log(divStyle.height);             //"100px"
	    console.log(divStyle.backgroundColor);    //"blue" 计算后内嵌样式表中的red被style特性中的blue覆盖
	
	    function getEleStyle(ele){
	        if(ele.currentStyle){
	            return ele.currentStyle;
	        }
	        else{
	            return document.defaultView.getComputedStyle(ele,null);
	        }
	</script>

对于"border"这样的综合属性，不同浏览器可能会也可能不会返回样式表中的实际规则，但对于`borderLeftWidth`所有浏览器都会返回。

**特别重要的一点**：所有计算后的样式是只读的，不能通过js去修改计算后样式对象中国的css属性。

###操作样式表

`document.styleSheets`包含了html中所有的引入的外部样式表以及嵌入式样式，可通过方括号+索引或者.item(i)的方式来访问cssStyleSheet对象。还有一种方式获得cssStyleSheet对象，通过`<link>`或`<style>`元素直接取得。
	
	function getCssStyleSheet(ele){
	    return ele.sheet || ele.styleSheet   //IE不支持sheet属性，但支持一个类似的styleSheet属性
	    }
	var linkStyle = document.getElementByTagName('link')[0];
	var styleSheet = getCssStyleSheet(linkStyle);  

CssStyleSheets对象有很多属性和方法，·`insertRule(rule,index)`向样式表指定位置插入样式字符串，`deleteRule(index)`删除样式表指定位置的规则（IE对性支持的是addRule( )和removeRule( )）。较为繁琐，不推荐使用。
CssStyleSheet通过cssRules(IE下支持rules)访问样式表中的样式规则集合,cssRules对象表示集合中的每一条规则。cssRules有几个常用属性：

 - `cssText` 返回整条规则对应的文本(IE不支持)，这个属性与上面提到的style,cssText类似，不同的是前者是只读的，返回值包括选择符和包含样式文本的花括号，后者可以被重写，返回值只有样式信息。
 - `selectorText` 返回当前规则的选择符文本，这个属性也是只读的，只在opera中允许进行修改。
 - `style` 通过这个属性可以设置和访问规则中的特定样式

          

          
