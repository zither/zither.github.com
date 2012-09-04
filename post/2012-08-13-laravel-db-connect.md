-----
{
    "title":"Laravel中的DB连接",
    "category":"Laravel"
}
-----

<p class="meta">13 Aug 2012 - By MiniMee</p>

Laravel使用PDO为我们提供了非常方便的DB类，我们在需要数据库支持时只需修改Config/database.php中的相关参数即可。我们以Mysql为例：

    'mysql' => array(
            'driver'   => 'mysql',
            'host'     => 'localhost',
            'database' => 'laravel',
            'username' => 'root',
            'password' => '123456',
            'charset'  => 'utf8',
            'prefix'   => '',
            ),

在connection数组中列出需要填写的数据库信息，如果是数据库在本地的话，这样填写肯定没有问题，但是用到网络数据库时就会出现问题，通常在使用非本地数据库时我们得到的host信息是IP和端口：

    mysql://127.0.0.1:3306/

在Laravel框架里不能直接把host填为xxx.xxx.xxx.xxx:3306。如果使用的是默认的3306端口只需要在host填上IP即可，PDO会使用默认端口，但是如果我们使用了其他端口，那么就必须在数据库参数中添加一个'port'参数：

    'mysql' => array(
            'driver'   => 'mysql',
            'host'     => '127.0.0.1',
            'port'     => '3307',
                    ......
            ),

这里也算上次所说的框架人性化的细节问题。
