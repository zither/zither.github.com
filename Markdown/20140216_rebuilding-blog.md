{
	"type": "article",
	"title": "重新整理博客",
	"url": "rebuild-blog",
	"tag": "Note",
	"category": "Life",
	"keywords": null,
	"date": "2014-02-16",
	"time": "15:10:04",
	"message": true,
	"publish": true
}

距离上次写博客已经是很久以前的事情了，因为放弃使用 Laravel 框架之后也就再也没有使用自己为 Laravel 框架写的静态博客插件。最近有一些学习上的东西想要记录一下，所以又切换到之前介绍过的 Pointless 静态博客程序。不过值得庆幸的是 Pointless 直到现在都还在更新维护，所以提交了一个半角双引号可能引起问题的 Issue。

<!--more-->

目前版本的 Pointless 和我一年多前用过的有蛮大的差别。大致浏览了一下程序目录，发现在自定义模板这部分有些不太直观，我直接去掉了 side 部分，然后把 comment 链接移到了 Tag 之后。暂时先这样用着，以后重新修改一个更简单点的模板。

稍微调整了下博客的主题，因为一贯喜欢简洁的页面，所以直接在官方 Classic 主题上做了减法，去掉了所有用不掉的元素。但是现在还没对引用和代码块做样式上的修改。另外有点想调整下 markdown 语法，改为更加接近 Github Flavored Markdown 的语法。搜索了一下，发现一个名叫 [parsedown](http://parsedown.org) 的解析库。
