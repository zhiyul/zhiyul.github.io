---
title: '监听DOM节点变化'
layout: post
guid: urn:uuid:2e14cdc3-c579-483e-b441-3d95ce389d02
tags:
    - javascript
---
最近在做一个chrome插件——1Player for 网易云音乐。

有一个功能把网页上个歌词显示在插件的popup里面。我的初步想法是在初始化popup或者歌曲切换的时候，去读取云音乐歌词面板里面的内容。但是网易云的歌词是异步加载的，也就是说就算歌曲已经切换并且播放，但是切换时候获取到的歌词还是空的或者是上一首的歌词。

我的第一个想法是在初始化或者切换歌曲的时候跑一个定时器去取歌词，然后和保存的上一次的歌词进行对比，不同的话就替换到现在显示的歌词。但是这样频繁地去查询DOM节点并不是很优雅。那么能不能去监听歌词面板的节点变化呢，当歌词变化的时候一定有节点被删除或者插入。所以只要能监听到这个事件，就可以不用定时器而只进行一次DOM查询。

顺着这个思路，google找到了这么一个API：[MutationObserver](https://developer.mozilla.org/en/docs/Web/API/MutationObserver)

> `MutationObserver`给开发者们提供了一种能在某个范围内的DOM树发生变化时作出适当反应的能力

看描述很符合我的需求，研究一番之后，明白了基本的用法：

1. 调用`MutationObserver`的构造函数，参数传入一个函数，这个函数会在指定的DOM发生变化时被调用，也就是说可以在这个函数里进行歌词提取的动作；
2. 调用实例上的`observe`方法，传入需要监听的DOM节点和一个初始化对象`MutationObserverInit options`——这是一个用来配置观察者对象行为的对象，根据需要选择把`childList`设置为true就可以观察目标元素新增或者移除了某个子节点（另外还有`attributes`和`characterData`两种节点变化，视具体情况采用）。另外一个属性`subtree`设置为true之后同时还会观察子元素的上述节点变化，在这里我只需要监听歌词面板的子元素增删，并不需要再对子元素的子元素增删监听；
3. 监听的节点的子节点发生增删后，会触发初始化的时候传入的回调函数，并传递一个``MutationRecord`对象数组给函数。为了确保是因为子节点的增删触发的回调函数，可以检查这个对象里的`addedNodes`和`removedNodes`（包含被添加/删除的节点）是否不为空来确认；
4. 调用实例方法`disconnect`停止监听目标DOM的变化。当然我需要一直监听，不调用这个方法。

综上撸一个函数来完成这些工作：

````javascript
observeDOM: (function(){
            var MutationObserver = window.MutationObserver || window.WebKitMutationObserver；
            return function(obj, callback){
                if( MutationObserver ){
                    var obs = new MutationObserver(function(mutations, observer){
                      	//确保是因为子节点的增删而触发
                        if( mutations[0].addedNodes.length || mutations[0].removedNodes.length )
                            callback();
                    });
                  	//开始监听子节点的增删
                    obs.observe( obj, { childList:true});
                }else{
  				//用定时器模拟 或者 监听 Mutation events
			    }
            }
        })(),
````

`MutationObserver`的支持情况：

![image](http://7xls2e.com1.z0.glb.clouddn.com/weatherstar-post-observer-dom.png)

对于不支持`MutationObserver`的浏览器，只能用定时器的方法来解决了，或者你也可以看看[Mutation events](https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Mutation_events)。不过这个已经被W3C从标准中移除了，慎用。

