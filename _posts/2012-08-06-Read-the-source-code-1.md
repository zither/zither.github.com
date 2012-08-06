---
layout: post
title: Laravel源码笔记(1)
---

{{ page.title }}
================

<p class="meta">06 Aug 2012 - By MiniMee</p>

无聊的时候看下Laravel的源码，前面写过一篇打印hello，world的文章，现在来看看到底是怎么完成的，随便可以熟悉下laravel框架的运行。

在框架的入口文件中主要进行了两个操作：
    require '../paths.php';
    require path('sys').'laravel.php';
那就从这里入手看看有什么好玩的东西。打开paths.php文件，里面最重要的是定义了一个path函数：
    function path($path)
    {
        return $GLOBALS['laravel_paths'][$path];
    }
这个函数主要是取得laravel框架的目录路径，在其他文件里会多次用到。另外在paths文件里定义了框架的app、sys、public等路径。入口文件中加载的另一个文件：laravel.php则是框架的主要文件。
    require  'core.php';
在laravel文件中首先载入的是框架的核心文件，如果有印象的话，在Artisan命令行工具中同样是加载了它，也就是说，如果我们理解了core文件，那么就知道了laravel框架的运行路线。
