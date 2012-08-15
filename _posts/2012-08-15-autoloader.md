---
layout: post
title: Laravel的自动加载
---

{{ page.title }}
================

<p class="meta">15 Aug 2012 - By MiniMee</p>

八月六日的时候写过一次关于[Laravel源码](/2012/08/06/Read-the-source-code-1.html)的文章，其实读懂一个项目的代码，必须事先了解它的自动加载机制，这里就接上篇文章说一下Laravel的自动加载。

Laravel中所有关于自己加载的部分都在laravel/autoloader.php文件中，从它声明的数组里我们就可以知道Laravel为我们提供的自动加载类型：

    public static $mappings = array();
    public static $directories = array();
    public static $namespaces = array();
    public static $underscored = array();
    public static $aliases = array();

我们可以加载单个文件($mappings)、一个目录($directories)、一个命名空间(namespace)。其中的$aliases数据则保存的是类的别名，在application/config/application.php中可以找到application插件包类别名的数组。而underscored则是为了对PHP5.3以下版本的兼容，是命名空间的替代方法。

    public static function load($class)
    {
        if (isset(static::$aliases[$class]))
        {
            return class_alias(static::$aliases[$class], $class);
        }
        elseif (isset(static::$mappings[$class]))
        {
            require static::$mappings[$class];

            return;
        }
        foreach (static::$namespaces as $namespace => $directory)
        {
            if (starts_with($class, $namespace))
            {
                return static::load_namespaced($class, $namespace, $directory);
            }
        }
        static::load_psr($class);
    }

查看Autoloader的load方法可以知道Laravel自动加载的判断规则。当加载一个类的时候，最先检查的是类别名，如果在别名数组中存在要加载的类，则直接声明一个别名。再检查文件数组，然后才是需要迭代才能判断的命名空间，最后是目录加载。

对于Laravel自动加载的使用方法可以参考[官方文档](http://laravel.com/docs/loading)写得很明白，如果想要进一步了解一些自动加载类的编写标准，可以阅读一下[PSR-0 Autoloading Standard](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md)
