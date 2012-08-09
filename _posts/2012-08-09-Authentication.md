---
layout: post
title: Authentication
---

{{ page.title }}
================

<p class="meta">09 Aug 2012 - By MiniMee</p>

Laravel框架自带了一个身份验证验证模块可以让我们方便的进行身份验证，使用方法在Laravel文档中还比较详细，这里我们只讨论一下相关设置：

    'driver' => 'eloquent',
    'username' => 'username',
    'password' => 'password',
    'model' => 'User',
    'table' => 'user'

在config/auth.php配置文件里一共有五个选项，第一个选项是你想要选择的驱动，Laravel提供了两种方式：‘fluent’和‘eloquent’。username和password是两种驱动都需要的参数。

如果你选择了fluent方式，那么你必须正确填写table选项，Laravel会直接在这个表中查询帐号和密码。

如果你选择了eloquent方式，那么你就需要model选项。就用User为例，你需要在models目录中建立一个user.php：

    class User extends Eloquent {}

eloquent会自动生成一个User类的实例，然后进行帐号和密码的匹配。但是我们要注意到是，eloquent默认查询的表为users表，如果你的用户表名不是users就会看到下面的错误信息：

    Message:
    SQLSTATE[42S02]: Base table or view not found: 1146 Table 'laravel.users' doesn't exist
    SQL: SELECT * FROM `users` WHERE `username` = ? LIMIT 1

错误提示的users表不存在。如果我们的用户表为admin，那么必须在User类中添加信息：

    class User extends Eloquent {
        public static $table = 'admin';
    }

更改之后就可以正确使用了。
