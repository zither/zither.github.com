-----
{
    "title": "Yield section",
    "category": "Laravel"
}
-----

<p class="meta">26 Aug 2012 - By MiniMee</p>

之前在寻找Section相关错误的时候在Section类中看到了下面这段代码：

    // laravel/section.php line 96
    protected function extend($section,$content)
    {
        if(isset(static::$sections[$section]))
        {
            static::$sections[$section] = str_replace('@parent',$content,
                    static::$sections[$section])
        }
        else
        {
            $static::$sections[$section] = $content;
        }
    }

我们可以看到代码中做了一次@parent替换，这段代码对应的就是关于yield section的操作内容。Blade模板中为我们提供了两种section操作：

    @yield('content') // yield对应的section
    @section('content')
        hello,world.
    @endsection

这是最常用的section用法，我们以用它来替换整段内容，另外一种即是yield section：

    @section('nav')  // 主模板内容
        <li>index</li>
    @yield_section
    @section('nav') // 子模板内容
        @parent
        <li>archive</li>
    @endsection

yield section语法会同时保留主模板和子模板的内容，我们可以把多个页面共同的部分提取出来，只在页面模板中保留各自独有的部分即可。

今天在修改Project Mee的view模板时不得已往模板里写入了原生的php代码，看起来非常的不雅观。因此以后在写视图时应该尽量把逻辑部分从中剔除。
