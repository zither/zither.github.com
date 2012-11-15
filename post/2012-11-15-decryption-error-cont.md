-----
{
    "title": "Decryption错误",
    "category": "Laravel"
}
-----

<p class="meta">15 Nov 2012 - By MiniMee</p>

在九月份的时候我发过一篇文章记录Laravel框架的Decryption错误：[Decryption error](http://blog.minimee.org/2012/09/22/decryption-error.html)。今天再次查看Laravel源码的时候发现我当时是这么修改的：

    // MB_STRING常量在laravel/core.php中定义
    if (MB_STRING) {
        // 如果使用了宽字符集，通过mb_substr截取字符
        $pad = ord(mb_substr($value, $length - 1, 1, Config::get('application.encoding')));
    } else {
        $pad = ord(substr($value, $length - 1));
    }

然后发现自己深深的范二了，遂再次修改一下：

    // MB_STRING常量在laravel/core.php中定义
    if (MB_STRING) {
        // 如果使用了宽字符集，通过mb_substr截取字符
        $pad = ord(mb_substr($value, -1, 1, Config::get('application.encoding')));
    } else {
        $pad = ord(substr($value, -1));
    }

以后尽量避免范这种思维短路的错误。

-- EOF --
