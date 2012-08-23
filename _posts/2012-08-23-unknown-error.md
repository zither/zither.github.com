---
layout: post
title: 一个很诡异的问题 | Laravel
---

{{ page.title }}
================

<p class="meta">23 Aug 2012 - By MiniMee</p>

在用Laravel编写静态博客插件的时候遇到了一个很诡异的问题，这里简单模拟一下问题出现的环境,首先建立连个简单blade模板页面：

    // application/views/mian.blade.php
    <html>
        @yield('content')
    </html>

    // application/views/post.blade.php
    @layout('main')
    @section('content')
        <p>This is post.</p>
    @endsection

    // application/views/page.blade.php
    @layout('main')
    @section('content')
        <p>This is page.</p>
    @endsection

然后直接建立一个简单的task：

    // application/tasks/io.php
    <?php 
    class Io_Task {

        public $path;

        public function __construct(){
            $this->path = path('base');
        }

        public function test(){
            $this->page();
            $this->post();
        }

        public function page(){
            $view = View::make('page');
            File::put($this->path.'/page.html',$view);
            $file = File::get($this->path.'/page.html');
            print_r($file);
        }

        public function post(){
            $view = View::make('post');
            File::put($this->path.'/post.html',$view);
            $file = File::get($this->path.'/post.html');
            print_r($file);
        }
    }

大致环境就是这样，现在直接运行artisan命令：

    php artisan io:test

本来预期的结果是下面这样：

    <html>
        <p>This is page.</p>
    </html>

    <html>
        <p>This is post.</p>
    </html>

但实际得到的结果却是：

    <html>
        <p>This is page.</p>
    </html>

    <html>
        <p>This is page.</p>
    </html>

从实际结果可以看出，生成的page和post页面内容竟然是一样的，都是第一次view的代码。我表示这个问题很诡异，又得去翻源代码。
