-----
{
    "title":"Laravel源码笔记",
    "category":"Laravel"
}
-----

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
在laravel文件中首先载入的是框架的核心文件，如果有印象的话，在Artisan命令行工具中同样是加载了它，也就是说，如果我们理解了core文件，那么就知道了laravel框架的运行路线。我们在这里设个断点，先去看看core文件。

在core文件中先是定义了命名空间Laravel和一系列常量，我们可以留意一下下面这句：
    define('DEFAULT_BUNDLE','application');
定义常量之后，用在paths文件中定义的path函数加载了六个系统文件：
    require path('sys').'ioc'.EXT;
    require path('sys').'event'.EXT;
    require path('sys').'bundle'.EXT;
    require path('sys').'config'.EXT;
    require path('sys').'helper'.EXT;
    require path('sys').'autoloader'.EXT;
暂时可以猜测这六个文件是框架依赖比较大的，然后我们要留意之后注册的一个自动加载函数:
    spl_autoload_register(array('Laravel\\Autolaoder','load'));
这个load函数应该是定义在上面加载的autoloader文件之中。
