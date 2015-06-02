---
layout: post
title: 自定义扩展fis的release命令，增加更新index.manifest时间戳的功能
categories: []
tags: []
published: True

---

最近在一个web app中使用fis作为构建工具，用起来很不错，常用功能齐全，比grunt的配置简单，中文文档写的很专业，团队其他小伙伴也很容易上手。谢谢百度前端团队。

我在web app中使用了离线缓存，每次代码修改都需要更新manifest文件的时间戳，这个工作手工做比较无趣，而且容易忘记，于是扩展了一下release命令，代码如下：

在option中增加－M配置：

	.option('-M, --manifest <file>', 'generate manifest file')

在action中实现：

	// 修改manifest时间戳
    if(options.manifest){
        // 获取manifest文件路径
        var manifest = fis.util.realpath(options.manifest);
        //异步读写
        var fs=require('fs');
                               
         //读取文件
         fs.readFile(manifest,'utf8',function(err,data){
            if(err){
                return console.log(err);
            }
            // 按照特征字符获取时间戳
            var start = data.indexOf("CACHE MANIFEST");
            var end = data.indexOf("CACHE:");

            // 生成新的时间戳
            var prefix = "CACHE MANIFEST \n#"+new Date().toString()+"\n\n";
            // 拼接新的文件内容
            var newContent = prefix+data.substring(end);
            console.log(newContent)
            
            // 写入文件 
            fs.writeFile(manifest, newContent, function(err) {
                if(err) {
                    return console.log(err);
                }
                console.log('generate manifest file done');
            });
         })
    }

使用方法很简单，使用－M加上manifest文件路径即可：

	fis release -d mobile -p -M ../mobile/index.manifest

更新的时间戳长这样：

	CACHE MANIFEST 
	#Thu May 28 2015 15:57:50 GMT+0800 (CST)

	CACHE:
	#index.html
    lib/css/lib.css
    lib/js/lib.js

怎么样，效果不错吧。分享出来，希望能给有类似需求的同学有所启发。
