-----
{
    "title": "finfo_open in windows",
    "category": "PHP"
}
-----

<p class="meta">28 Aug 2012 - By MiniMee</p>

由于一些原因，我现在大部分时间都是在windows下写代码，用Appserv搭建了简单的PHP环境，并把PHP升级到了5.4.5版本。

之前记得fileinfo拓展在PHP5.3版以上是默认集成的，所以在程序中就直接使用了finfo_open函数，但是却遇到了函数未定义错误：

    Fatal error: Call to undefined function finfo_open() 

然后去翻了下php.ini中的拓展信息，发现在windows环境下并没有关于fileinfo拓展的相关语句，于是手动加上了设置：

    extension=php_fileinfo.dll

随后程序能够正确运行了，只是想不通为什么windows的php.ini中没有相关设置。查看了下[fileinfo的官方信息](http://www.php.net/manual/en/fileinfo.installation.php)，果然看到下面的提示：

    Windows users must include the bundled php_fileinfo.dll DLL file 
    in php.ini to enable this extension.
