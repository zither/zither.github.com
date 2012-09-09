-----
{
    "title": "Artisan bundle",
    "category": "Laravel"
}
-----

<p class="meta">09 sep 2012 - By MiniMee</p>

记录一个关于Laravel插件包的问题。在本地开发项目的时候都是直接使用命令行来运行artisan，但是在虚拟机上是无法直接使用artisan，所有找到了一个artisan的在线插件包：

    https://github.com/joecwallace/laravel-artisan-bundle

在功能上来说可以完全满足我的需求，但是在使用上还是有一点点问题，这个问题可能并非插件包的问题。

我还特意把插件包还上传到Openshift的环境测试了一下。Openshift无法更改主机目录，所以我用了rewrite把请求重定向了Public/index.php/。

假如使用artisan插件包的时候直接访问链接:

    http://minimee.org/artisan/migrate

会直接提示404错误，但是如果把插件包重命名为其他，又可以成功访问。比如改名为hello，那么访问：

    http://minimee.org/hello/migrate

会在浏览器中看到artisan成功运行的提示：

    Equivalent command:
    php artisan migrate
    Output:
    No outstanding migrations.

之前在看Laravel框架文档的时候并没有预留关键词的印象，我表示这个问题很诡异，先记录下来，有时间再看源代码。

-- EOF --
