---
layout: post
title: 修正Chart.js在update时重置高亮状态的问题
categories: []
tags: [javascript,Chart.js]
published: True

---

近日在一个web app中使用了Chart.js，这个图表库胜在简洁小巧，各类型图表的文件也是模块化独立的，用在手机端很合适。

开发需求中用到了柱状图，点击某个柱子后高亮显示，这个效果是插件自带的，只需配置好highlightFill即可。但是当在页面其他部分点击时，插件默认会取消高亮效果。

开发需求中需要将点击的柱子一直高亮显示，于是需要手动更新柱子的颜色了，代码如下：

	myBarChart.datasets[0].bars[2].fillColor = "#dd0000";
	myBarChart.update();

当多次调用update方法时发现，update方法每次都会将上次设置的高亮颜色重置，这可要不得，影响我实现效果啊。

最后读了读Chart.Bar.js的源代码，发现了这个处理，在文件的144-147行：

	// Reset any highlight colours before updating.
	helpers.each(this.activeElements, function(activeElement){
		activeElement.restore(['fillColor', 'strokeColor']);
	});

将这几句注释掉，每次设置的高亮颜色都会被永久保留，此时柱子的颜色就完全由我手工控制了，哈哈哈。

为这个小事花了很长时间，稍作记录，聊以备忘。