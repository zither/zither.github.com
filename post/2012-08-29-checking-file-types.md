-----
{
    "title": "Checking file types",
    "category": "Laravel"
}
-----

<p class="meta">29 Aug 2012 - By MiniMee</p>

Laravel文档正在翻译中，第一次尝试翻译英文文档，进展非常缓慢。

今天翻译的是file类相关文档，就想写下file类提供的is方法。这个方法可以用于检测文件类型，使用方法如下：

    if(File::is('jpg','path/to/file.jpg')){
        // ....
    }

关于文件类型检测在很多地方都会用到，特别是在检测用户上传文件的时候。文档中对is方法做了一个简单的说明，is方法并不仅仅是简单的检查文件的拓展名。

我们来看看is方法的源代码：

	public static function is($extensions, $path)
	{
		$mimes = Config::get('mimes');

		$mime = finfo_file(finfo_open(FILEINFO_MIME_TYPE), $path);

		foreach ((array) $extensions as $extension)
		{
			if (isset($mimes[$extension]) and in_array($mime, (array) $mimes[$extension]))
			{
				return true;
			}
		}

		return false;
	}

源代码相当简单，我们可以很清楚的看到is方法检查了两个部分：

    // 获取在application/config/mimes.php中定义的MIME类型
    $mimes = Config::get('mimes');
    // 获取文件的真实MIME类型
    $mime = finfo_file(finfo_open(FILEINFO_MIME_TYPE),$path);

文档中说的不简单就是指的这里，函数的第一个参数用来获取应用程序预定义的MIME类型，第二个参数通过PHP的Fileinfo拓展来获取文件的真实MIME类型。被检查的文件只有同时满足两个条件函数才会返回TRUE：

    // 只有被检测文件的真实MIME类型在应用程序允许的MIME类型中时才会返回TRUE
    isset($mimes[$extension]) and in_array($mime, (array) $mimes[$extension])

所以如果想通过更改后缀名的方式来骗过程序是不行的啦。

--EOF--
