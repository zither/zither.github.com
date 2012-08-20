---
layout: post
title: unserialize error | Laravel
---

{{ page.title }}
================

<p class="meta">20 Aug 2012 - By MiniMee</p>

    unserialize(): Error at offset 0 of 224 bytes

这个估计是我接触laravel以来碰到次数最多的错误了。由于我经常测试不同的laravel程序，又没有专门为它们设置独立的虚拟机，每次替换程序都会遇到这个错误。

    E:\AppServ\www\laravel\session\drivers\cookie.php on line 24

从错误的location提示就知道问题出现在cookie上。你或许会问：我没有进行过cookie相关的操作，为什么还会碰到这个问题？

答案就是运行laravel时它会自动储存一个session值。这个错误的元凶在laravel/laravel.php文件中：

    // laravel/laravel.php line 151
    if (Config::get('session.driver') !== '')
    {
        Session::save();
    }

第一次运行laravel就会在这里保存一个session(默认情况下session.driver是cookie)。如果只是存储一个cookie也不会导致错误，（默认情况下）主要是每次运行laravel都会加载application/start.php文件：

    // application/start.php line 170
    if ( ! Request::cli() and Config::get('session.driver') !== '')
    {
        Session::load();
    }

在start.php文件中还进行了load()操作。引起错误的代码就在这两个地方，而导致错误的根本原因是我们更改了application/config/application.php中的key值。

laravel在保存session时会用application/config/application.php中的加密key进行加密操作，如果我们在保存cookie之后变更了加密key，那么之后再运行程序进行load操作就会decrypt出错。

这个错误的解决办法很简单，只需要清除一下cookie即可。
