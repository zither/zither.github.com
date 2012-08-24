---
layout: post
title: Section Error(续) | Laravel
---

{{ page.title }}
================

<p class="meta">24 Aug 2012 - By MiniMee</p>

继续写昨天遇到的Section Error，其实问题在昨天晚上临睡前已经解决，今天说下出现问题的原因。首先找到昨天出现问题的代码，所有的问题都集中在这几行行中：

    protected function page(){
        $view = View::make('page');
        File::put($this->path.'/page.html',$view);
    }
    protected function post(){
        $view = View::make('post');
        File::put($this->path.'/post.html',$view);
    }

在page和post函数中我做了两个操作，一是注册了相关的视图，二是把视图的结果分别存入了page.html和post.html中。View类的make方法其实并没有做过多的操作，查看下它的代码：

    // laravel/view.php line 202
    public static function make($view,$data = array()){
        return new static($view,$data);
    }
    // laravel/view.php line 79
    public function __construct($view,$data = array()){
        $this->view = $view;
        $this->data = $data;
        if(starts_with($view,'path: ')){
            $this->path = substr($view,6);
        }else{
            $this->path = $this->path($view);
        }
        .......
    }

结合源代码就很容易知道View类的make方法做了哪些操作，其实很简单，它生成了一个View类的实例，其中包括了视图的名称、数据、名称等等。

很明显问题不会出在这里，因此出现问题的只能是在剩下生成页面的代码上。之所以能把View类的实例用在File类的put方法中是因为View类中定义了tostring方法：

    // laravel/view.php line 554
    public function __toString(){
        return $this->render();
    }
    // laravel/view.php line 321
    public function render(){
        Event::fire("laravel.composing:{$this->view}",array($this));
        if(Event::listeners(static::engine)){
            $result = Event::until(static::engine,array($this));
            if( ! is_null($result))return $result;
        }
        return $this->get();
    }

从与render方法相关的代码中可以很容易找到关键性代码，在render方法中触发了视图引擎的事件：

    $result = Event::until(static::engin,array($this));

这个事件实在Blade类sharpen方法中监听的：

    // laravel/blade.php line 46
    Event::listen(View::engine,function($view)
            {
                ......
            });

这里不对blade类做过多说明，它主要负责把我在视图中的blade模板转换成php语言，例如mian模板和page模板会被转化成下面的样子：

    // mian.blade.php
    <html>
        <?php echo \Laravel\Section::yield('content'); ?>
    </html>
    // page.blade.php
    <?php \Laravel\Section::start('content'); ?>
    <p>This is page.</p>
    <?php \Laravel\Section::stop(); ?>
    <?php echo view('main')->with(get_defined_vars())->render(); ?>

正如你所看到的，Section才是真正的原因。page页和post页都被转换成了正确的php代码，我却得到了意外的返回，所有的问题都归于Section。

在Section类中声明了一个$secitons数组：

    // laravel/section.php line 10
    public static $sections = array();

在生成视图的过程中会依次执行模板中的代码，

    // laravel/section.php line 35
    public static function start($section,$content = ''){
        if($content === ''){
            ob_start() and static::$last[] = $section;
        }else{
            static::extend($section,$content);
        }
    }
    // laravel/section.php line 82
    public static function stop(){
        static::extend($last = array_pop(static::$last),ob_get_clean());
        return $last;
    }
    // laravel/section.php line 96
    protected static function extend($section,$content){
        if(isset(static::$sections[$section])){
            static::$sections[$section] = str_replace('@parent',$content,static::$sections[$section]);
        }else{
            static::$sections[$section] = $contetn;
        }
    }
    // laravel/section.php
    public static function yield($section){
        return (isset(static::$sections[$section])) ? static::$sections[$section] : '';
    }

看完代码的执行过程，相信就已经明白问题的根本了。模板中的page页和post页都保存在section('content')，当第一次运行section类时section类会把第一次运行时的content内容保存到$sections数组中。

例如先解析的page页，那么$sections数组中保存的就是‘This is page。’，那么再解析post页时extend函数就会检测static::$sections[$section]已经存在，就直接调用了page页的content内容。

从根本上说，这是一个关于PHP语言的生存周期的问题。解决方法很简单，但是却不够优雅：

    protected function page(){
        $view = View::make('page');
        File::put($this->path.'/page.html',$view);
        unset(Section::$sections['content']);
    }
    protected function post(){
        $view = View::make('post');
        File::put($this->path.'/post.html',$view);
        unset(Section::$sections['content']);
    }

除了手动释放变量外，还有一个不是办法的办法，那就是page页和post页使用不同的section，但是也就失去了使用模板的意义了。
