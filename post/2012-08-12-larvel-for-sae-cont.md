-----
{
    "title": "在SAE中使用Laravel(续)",
    "category": "Laravel"
}
-----

<p class="meta">12 Aug 2012 - By MiniMee</p>

在前一篇中讲到了两种存在问题的修改方法，因为原生视图和blade视图不能共用存在，现在我们来改进一下方法，让我们能够同时使用两种模板。

我们想简单说下Laravel框架中使用视图的方法，我们在application的路由里简单的注册一个视图：

    Route::get('/',function()
            {
                return View::make('home.index');
            });

我们直接忽略GET请求，只关注视图部分。查看larvel/view.php源文件我们可以知道，上面代码中的make方法保存视图的名字，数据以及路径：

    // make方法直接生成了一个view类的示例
    public static function make($view, $data = array())
    {
        return new static($view, $data);
    }
    // 视图的相关数据分别储存在$this->view,$this->data,$this->path中。
	public function __construct($view, $data = array())
	{
		$this->view = $view;
		$this->data = $data;
		if (starts_with($view, 'path: '))
		{
			$this->path = substr($view, 6);
		}
		else
		{
			$this->path = $this->path($view);
		}
        .....
    }

这里保存的数据后面都会用到。Laravel框架是怎么区分原生视图和blade模板的呢？我们在application/start.php文件里可以找到答案：

    // application/start.php 第142行
    Blade::sharpen();

我们在start.php文件里启用了Blade。我们查看laravel/blade.php文件可以知道他是怎么判断模板的：

    public static function sharpen()
    {
        Event::listen(View::engine, function($view)
                {
                    if ( ! str_contains($view->path, BLADE_EXT))
                    {
                        return;
                    }
                    $compiled = Blade::compiled($view->path);
                    if ( ! file_exists($compiled) or Blade::expired($view->view, $view->path))
                    {
                        file_put_contents($compiled, Blade::compile($view));
                    }
                    $view->path = $compiled;
                    return $view->get();
                });
    }

在sharpen方法中可以看到，它监听了一个视图类的模板引擎，而我们关心的区分方法就在匿名函数中。它调用了我们注册视图时保存的视图路径数据，然后直接在路径里查找blade后缀来判断。如果是blade模板就进行渲染然后把处理过后的模板路径储存到视图path里。然后直接get获取数据。

我们回过头去看视图的get方法，那里可以找到我们需要修改的部分：

    // application/view.php 第345行
    $__contents = $this->load();

get方法里最重要的就是这句，load()函数决定了加载内容：

    protected function load()
    {
        if (isset(static::$cache[$this->path]))
        {
            return static::$cache[$this->path];
        }
        else
        {
            return static::$cache[$this->path] = file_get_contents($this->path);
        }
    }

我们可以看出，load函数里通过file_get_contents方法载入了路径为$this->path的模板内容。现在我们大致看完了Laravel是怎么载入视图文件的，也大致知道该怎么修改了。

修改的关键在于储存视图路径的path变量，只要我们把两种视图路径分开保存，然后分开加载，那么所有的问题都解决了.

我们现在开始修改，首先在laravel/view.php类中为blade模板添加一个专门的路径：

    public $sae_path;

修改laravel/view.php类的construct方法：

    if (starts_with($view, 'path: '))
    {
        $this->path = substr($view, 6);
    }
    else
    {
        if(strpos($this->path($view),'blade'))
        {
            $this->sae_path = $this->path($view);
        }
        else
        {
            $this->path = $this->path($view);
        }
    }

然后修改laravel/blade.php的sharpen方法：

    public static function sharpen()
    {
        Event::listen(View::engine, function($view)
                {
                    if ( ! isset($view->sea_path))
                    {
                        return;
                    }
                    $compiled = Blade::compiled($view->sae_path);
                    if ( ! file_exists($compiled) or Blade::expired($view->view, $view->path))
                    {
                        file_put_contents('saemc://'.$compiled, Blade::compile($view));
                    }
                    $view->sae_path = $compiled;
                    return $view->get();
                });
    }

最后修改laravel/view.php中的load方法：

    protected function load()
    {

        if (isset(static::$cache[$this->path]))
        {
            return static::$cache[$this->path];
        }
        elseif (isset(static::$cache[$this->sae_path]))
        {
            return static::$cache[$this->sae_path];
        }
        else
        {
            if (isset($this->sae_path))
            {
                return static::$cache[$this->sae_path] = file_get_contents('saemc://'.$this->sae_path);

            }
            return static::$cache[$this->path] = file_get_contents($this->path);
        }
    }

现在我们就可以在SAE中同时使用原生视图和blade模板了。

Demo：[http://laravel.sinaapp.com/](http://laravel.sinaapp.com/)
