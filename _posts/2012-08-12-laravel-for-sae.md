---
layout: post
title: 在SAE中使用Laravel
---

{{ page.title }}
================

<p class="meta">12 Aug 2012 - By MiniMee</p>

前段时间我很想把Laravel框架的手册翻译成为中文，但是试着翻译了几篇，发现自己的表达能力实在不敢恭维，完全与信达雅沾不上边。为了不误人子弟就先把这个项目放下，转而着手写一个Laravel框架的入门教程。

这个入门教程要写的是怎么用Laravel框架建立一个简单的网站，目前示例网站已经建好了，但是由于我自己的主机使用的是PHP5.2，所以不能把教程放在自己主机上。寻找了一圈比较流行的云服务器，比较满意并且能够使用Laravel的只有新浪SAE和红帽的Openshift。

Openshift的自由度是最大的，能够使用原生的PHP，这个很容易。而新浪的SAE虽然用的是PHP5.3以上的版本，但是禁用了部分函数，必须对Laravel框架进行移植才行。最后我把[Laravel入门](http://laravel.minimee.org/)托管在了Openshift上了。

由于Openshift服务器在国外，为了防止意外，也把Laravel移植到了SAE上。不过暂时只是做了简单的修改，只测试了我需要使用的功能，其他问题必须遇见了才能修改。

SAE环境和原生PHP有很多不同，在移植方面最大的问题应该是文件I/O上，SAE采用了分布式，每次访问的主机都有可能不同，因此SAE禁止了本地文件的读写。但是如果使用了Laravel的模板功能，就会出想写入错误。问题主要在这两个函数上：

    file_get_contents($path);
    file_put_contents($path);

在SAE中可以使用file_get_contents函数，但是不能使用原生的file_put_contents函数，因此最不是办法的办法就是直接用原生PHP写视图，不用file_put_contents就不会出错。

如果你要使用blade模板文件，那就必须修改框架。如果你用的是blade模板，完全没用原生视图，那修改方法也很简单，先修改laravel/view.php文件：

    // laravel/view.php 第381行中添加'saemc://':
    protected function load()
    {
        if (isset(static::$cache[$this->path]))
        {
            return static::$cache[$this->path];
        }
        else
        {
            return static::$cache[$this->path] = file_get_contents('saemc://'.$this->path);
        }
    }

然后再修改laravel/blade.php:

    // laravel/blade.php 第63行中添加'saemc://':
    if ( ! file_exists($compiled) or Blade::expired($view->view, $view->path))
    {
        file_put_contents('saemc://'.$compiled, Blade::compile($view));
    }

需要注意的是，如果使用了saemc，那么就必须在SAE中开启*Memcache*服务。

这两种方法虽然容易，但是这样修改就相当于限制了laravel框架的功能，不推荐使用。在下一篇中我们来修改Laravel框架让它在SAE中能同时支持两种视图模式。
