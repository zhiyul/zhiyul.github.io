---
title: 'jPopupbox编写总结'
layout: post
guid: urn:uuid:C09C689A-E2FE-547F-D159-DC5EE84C60B7
tags:
    - javascript
---
####遇到的一些问题和技巧： 

 1. 事件绑定：可以将事件类型的事件处理函数保存在dom对象的自定义事件属性上，这样解除绑定就非常方便

    el.bindFn = el.bindFn || {};    //在element对象上定义一个绑定的事件的内容属性
        el.bindFn[identifier] = {
            eventType : type,
            eventFn : fn
        }；                                  
    el.addEventListener(type,fn,false);    //绑定
    el.removeEventListener( el.bindFn[identifier].eventType, el.bindFn[identifier].eventFn,false);//解绑

很方便。
 2. 对元素class的一些操作：html5提供了classList属性，利用它的方法可以方便地对元素的class进行增删改查

    ele.classList.add(className)       //将给定的字符串值添加到列表中
    ele.classList.contains(Name)       //列表中如果存在给定的值返回ture，否则false
    ele.classList.remove(className) //从列表中删除给定的字符串
    ele.classList.toggle(className)  // 如果列表中存在给定的值，删除它；不存在则添加他

对于不支持这一属性的浏览器，要用点别的办法，略微麻烦。添加比较简单，将需要添加的class拼接在ele,className之后就可以了，注意之间要留一个空格。
删除和查找和替换有两种方式

    （1）将ele.className用split（’‘）拆分为数组，遍历数组，找到目标class对其操作
    （2）使用正则表达式匹配ele.className,匹配项为'(\\s|^)' + className + '(\\s|$)'

 3. 将生成的tipbox添加到dom中并使用toggleClass->“hide”的方式控制tipbox的显隐，在ie6-8下会出现无法获取tipbox宽高的bug，解决方式，使用ele.style.display=none/block来控制。
 4. 在浏览器标准模式下，不同浏览器页面的滚动大小获取方式不一致，chrome下document.body.scrollTop为页面滚动高度，document.documentElement.scrollTop为0，而ff下document.body.scrollTop为0，document.documentElement.scrollTop为页面滚动高度，其他浏览器中不论是那种，两者总有一个为0，所以得到正确的值将两者相加即可。
 
    if(document.compatMode == 'BackCompat'){
            return {
            'scrollTop' : document.body.scrollTop,
            'scrollLeft' : document.body.scrollLeft
            };
    }
    else{
        return {
        'scrollTop' : document.body.scrollTop+document.documentElement.scrollTop,
        'scrollLeft' : document.body.scrollLeft+document.documentElement.scrollLeft
        };           
    }