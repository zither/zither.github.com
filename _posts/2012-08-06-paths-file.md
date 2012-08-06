---
layout: post
title: Paths file
---

{{ page.title }}
================

<p class="meta">06 Aug 2012 - By MiniMee</p>

无聊的时候看下Laravel的源码，前面写过一篇打印hello，world的文章，现在来看看到底是怎么完成的，随便可以熟悉下laravel框架的运行。

在框架的入口文件中主要进行了两个操作：
    require '../paths.php';
    require path('sys').'laravel.php';
那就从这里入手看看有什么好玩的东西。
