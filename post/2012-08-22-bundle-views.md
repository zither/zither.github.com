-----
{
    "title":"Bundle views",
    "category":"Laravel"
}
-----

<p class="meta">22 Aug 2012 - By MiniMee</p>

记录一个小疏忽，今天在写laravel插件包的时候用到了bundle view，view中使用的是blade模板：

    @layout('main')
    @section('content')
        {{ $content }}
    @endsection

在post页面习惯性的使用了@layout('main')，因为不是写的web页面而是一直在命令行下，因此也没有看到laravel关于错误的提示。一个简单的疏忽却用了不少的时间才找到原因，这个需要引以为戒。

其实在bundle中如果要用到view，在blade模板里引用layout时必须说明bundle名：

    @layout('bundle::main')
    @section('content')
        {{ $content }}
    @endsection

另外想说一下昨天提到的静态博客程序。如果把需要的文章title和category以数组的形式储存在laravel的config目录中，那么在生成静态页面的时候就不再需要遍历整个文章目录。

并且laravel为我们提供了一个非常有用的config类，假设title和category以下面的形式储存：

    // bundle/blog/config/category.php
    <?php
    return array(
            'life' => array(
                    'slug' => 'title',
                    'slug' => 'title'
                ),
            'work' => array(
                    'slug' => 'title',
                    'slug' => 'title'
                ),
            );

那么当需要分类数据的时候就直接使用config类来提取：

    // 获取全部分类数据
    $category = Config::get('blog::category');
    // 获取单个分类数据
    $category = Config::get('blog::category.work');

读取单个文件的效率肯定要比遍历整个目录的效率高，相当于使用了一个简单的文本数据库。
