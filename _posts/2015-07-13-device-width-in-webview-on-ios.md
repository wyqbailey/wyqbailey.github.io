---
layout: post
title: 移动设备上webview的device-width宽度问题
categories: []
tags: [ios,javascript]
published: True

---

在web app中，我们一般都会设置viewport的宽度为设备宽度，即：

	<meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no"/>

但如果web页面是嵌入在webview中，而webview控件的宽度并不是全屏，那该页面的device-width的宽度是多少呢？

经过实际测试，发现Android和iOS对这个问题的处理不一样：

Android认为嵌入页面的device-width是webview的宽度，比如手机屏幕宽度为320，而webview的宽度为280，则webview嵌入页面的device-width就是280。这个处理虽然偏离了device-width的词面含义，但是对web开发还是很有好处，不用担心可视窗口与viewport宽度不一致的情况。

iOS严格遵循了device-width的词面含义，无论webview的宽度是多少，嵌入页面的device-width永远都是设备宽度。但这给开发带来了不便，嵌入页面需要根据webview的实际宽度来设置viewport的宽度。如果webview的宽度不是固定值，而是保留了左右边距的自适应值（此时webview在iphone4/5/6上的宽度会不一致），那我们还需要使用js做一下计算才行：
	
	if(ios){
		var viewwidth = $(window).width()-2*30; //假设边距值是30
		$("meta[name=viewport]").attr("content","width="+viewwidth+", initial-scale=1.0, maximum-scale=1.0, user-scalable=0");
	}