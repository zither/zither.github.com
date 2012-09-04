-----
{
    "title": "Base Controller",
    "category": "Laravel"
}
-----

<p class="meta">04 sep 2012 - By MiniMee</p>

我们在Laravel中建立控制器的时候都会这样的方式开头：

    class Index_Controller extends Base_Controller {

        /**
         * @brief 首页操作，返回index视图
         *
         * @return object
         */
        public function action_index()
        {
            /** 从config/site.php获取网站名称 */             
            $site_name = Config::get('site.name');

            return View::make('index',array(
                        'site_name' => $site_name
                        ));
        }
    }

所有自定义的控制器都继承于Base_Controller,其实Base_Controller的代码相当简单，只定义了一个魔术方法：

    public function __call($method, $parameters)
	{
		return Response::error('404');
	}

这让我想起了自己第一次写框架时的笨办法，总喜欢用各种isset来检测控制器和方法。

另外想提一下路由和控制器之间的一点差别。路由可以完成控制器所能做到的事情，但是控制器却不能。举一个简单的例子,对于首页的路由规则我们可以这样写：

    Route::get('/',function()
            {
                //
            });

控制器是不能单独完成这项任务的，我们只能这样写：

    Route::get('/','index@index');

你可以看到，我们必须注册一条'/'路由规则，并把它指向控制器的某个方法。

-- EOF --
