-----
{
    "title":"Laravel绑定视图数据",
    "category":"Laravel"
}
-----

<p class="meta">11 Aug 2012 - By MiniMee</p>

Laravel提供了4中方法来绑定视图数据，前两种非常好理解，直接在路由或者控制器里使用：

    // with方法
    View::make('home')->with('name', 'James');
    // 数组
    View::make('home', array('name' => 'James'));

with方法和数组传递都非常直观，但是其他两种的使用方法在手册里写得非常简陋：

    // 属性形式
    $view->email = 'example@example.com';
    // 数组形式
    $view['email'] = 'example@example.com';

如果是第一次接触到Laravel框架，对于上面两句话肯定觉得莫名其妙，需要想下才能反映过来。其实从这样的细节就可以看出框架是否人性化。框架手册对于新手来说非常重要应该保持浅显易懂，不应该过分简略。

最后两种方法的使用方法应该是这样的：

    $view = View::make('home');
    // 属性形式
    $view->email = 'example@example.com';
    // 数组形式
    $view['email'] = 'example@example.com';
    return $view;

只需要简单的加上两句代码就可以清晰的说明用法，为什么偏偏就要省略呢。为此刚才我还去翻看了Laravel 2.x版本的手册，发现这段代码在2.x版手册里是完整的，费解。

最后还想说的是，这样模糊不清的地方在手册里不止一处。
