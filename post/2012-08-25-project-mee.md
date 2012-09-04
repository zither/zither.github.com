-----
{
    "title":"Project Mee",
    "category":"Portfolio"
}
-----

<p class="meta">25 Aug 2012 - By MiniMee</p>

大概用了3天时间写了这个基于Laravel框架的静态博客插件，虽然还没有重构代码，但是起码能够正常运行了。沿用之前一贯的风格，我把这个静态博客插件取名为"Porject Mee"。

Project Mee是基于Laravel框架的插件包，通过Aartisan命令行工具运行，只提供最简单的命令：

    php artisan mee::io:article  // 新建一篇文章
    php artisan mee::io:page     // 新建一个页面
    php artisan mee::io:pub      // 发布最新文章
    php artisan mee::io:reb      // 重新发布所有内容

Project Mee的目录结构和其他Laravel插件包类似，新增了几个需求文件夹：

    Mee
    ├── start.php       『插件启动文件』
    ├── Config          『设置目录，包含博客设置及文章数据』
    ├── Libraries       『Porject Mee所用到的类文件』
    ├── Tasks           『命令行工具目录』
    ├── Views           『视图模板文件夹』
    ├── post            『博客文章Markdown文件』
    ├── page            『博客页面Markdown文件』
    └── Site            『静态网站生成目录』

Project Mee借鉴了PHP静态博客程序[Pointless](http://scar.simcz.tw/article/2012/05/07/pointless-readme/) 的正则表达式，在此表示感谢。

已经把Project Mee的最初版本托管到了Github上，现在的代码非常粗糙，很多地方需要重构。

- [Project-Mee@Github.com](https://github.com/zither/project-mee)
