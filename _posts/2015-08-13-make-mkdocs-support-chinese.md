---
layout: post
title: 让mkdocs支持中文搜索
categories: []
tags: []
published: True

---

mkdocs是一个很方便的文档网站生成工具，通过简易的配置和markdown文件就可以生成很美观专业的文档网站。效果请参考：http://www.mkdocs.org/。

mkdocs自带搜索功能，可以根据全站导航进行搜索，很方便，唯一不足的是不支持中文搜索。这点不足让我很是不爽，于是自己动手解决问题。

经过一系列艰苦卓绝的追踪分析，发现mkdocs不支持中文搜索的原因是：

* 搜索功能使用了lunr.js（一个js的分词搜索引擎），这玩意目前不支持中文。
* mkdocs工具在build时生成的搜索索引文件（json格式），中文被转成了ascii码，搜索的数据源直接就是乱码

为了解决这两个问题，很费了一些时间和视力，顺便了解了一下python。解决方法如下：

* 使用国内同仁修改过的支持中文的lunr.js（https://github.com/ming300/lunr.js），记得star一下。
* 修改mkdocs的search文件（mkdocs/search.py）,修改generate_search_index方法：


		return json.dumps(page_dicts, sort_keys=True,ensure_ascii=False, indent=4)

增加了`ensure_ascii=False`设置，禁止将中文字符转为ascii码，这样输出的json就支持中文了。

修改完之后，使用`python -m py_compile search.py`命令编译一下search文件，然后将新的`search.py`和`search.pyc`（编译文件）替换掉旧的即可。

注：mkdocs的安装目录在`/Library/Python/2.7/site-packages/mkdocs/`

修改完成之后，`mkdocs build`一下，看看效果吧！