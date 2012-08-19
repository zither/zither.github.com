---
layout: post
title: 静态博客程序Pointless | PHP
---

{{ page.title }}
================

<p class="meta">19 Aug 2012 - By MiniMee</p>

昨天介绍了kudos博客程序，但是它在中文标题上有很大的硬伤。除了kudos之外之前还留意过一个博客程序Pointless，Pointless是台湾人用PHP写的静态博客程序，同样是采用Markdown文本存储。在线Demo直接访问作者网站：[ScarShow](http://scar.simcz.tw/)。

需要说明的是Pointless使用PHP-CLI命令模式，没有可视化界面，可以直接从[Github](https://github.com/scarwu/Pointless)下载程序。

这里简单说下Pointless在windows下的使用方法。首页确保PHP的BIN目录已经加入到了系统的环境变量中，windows中我们需要用cmd来运行Pointless。

clone或者下载Pointless程序到任意目录，然后打开cmd命令工具，cd路径到pointless的bin目录下，如e:/pointless/bin/，运行命令：

    php poi init

运行之后pointless会在目录里生成一个blog文件夹，博客的相关设置都保存在blog/config.php中，可以按需修改，其中ARTICLE_URL表示你想要的文章固定链接形式。Pointless提供了一个简单的帮助提示：

    php poi help

它会列出所有Pointless支持的命令，命令非常简单：

    init/gen/gen css/gen js/gen clean/article/article add/blogpage/blogpage add

如果我们现在要写博客，只需运行命令：

    php poi article add

运行命令之后会要求输入相关的参数，比如文章的title、url、分类等。输入完毕之后会自动在blog/markdown/article/下创建一个文本，内容大致如下：

    -----
    {
        "title": "hello world",
        "url": "hello-world",
        "tag": "php",
        "category": "life",
        "date": "2012-08-19",
        "time": "14:44:25"
    }
    -----

我们可以修改其中的参数，也可以直接在下面用markdown语法写博客，写完保存之后再运行命令：

    php poi gen

Pointless会自动用所有博客文件在blog/public下生成一个静态网站，我们只需要把public目录下的静态文件上传到服务器即可。Pointless作者也写了一篇简单的教程，可以去看一看：[Pointless使用說明](http://scar.simcz.tw/article/2012/05/07/pointless-readme/)。

最后仍然要说一下缺点，我测试使用的是windows环境，仍然存在文章名中文乱码的问题。这个跟所使用的文件系统有关，add article时从CMD中输入中文名应该是GBK编码，如果直接用gen命令生成静态页面之后会出现乱码。

简单Hack下解决问题，直接修改源文件：

    // command/pointless/article/add.php line-14
    while('' == $info['title'] = iconv('GBK','UTF-8',NanoIO::Read()));

直接把中文title转为UTF-8编码，然后在生成静态网站就不会出错了。除了文章名之外，强烈建议其他选项全部都使用英文，包括url、tag、category,以免出现各种编码问题。
