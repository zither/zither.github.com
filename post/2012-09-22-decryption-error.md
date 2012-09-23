-----
{
    "title": "Decryption error",
    "category": "Laravel"
}
-----

<p class="meta">22 sep 2012 - By MiniMee</p>

之前在文章[Redirecting with data](http://blog.minimee.org/2012/09/05/redirecting-with-data.html)中提到在Validation类中使用中文信息会出现一个Decryption error:Padding is invalid错误。

这个错误提示是定义在Laravle框架的Crypter类中的：

    // laravel/crypter.php line 130
	protected static function unpad($value)
	{
		$pad = ord($value[($length = Str::length($value)) - 1]);

		if ($pad and $pad < static::$block)
		{
			if (preg_match('/'.chr($pad).'{'.$pad.'}$/', $value))
			{
				return substr($value, 0, $length - $pad);
			}
			else
			{
				throw new \Exception("Decryption error. Padding is invalid.");
			}
		}

		return $value;
	}

Crypter类是Laravel框架的加密类。Laravel在加密的时候会进行一下几个步骤：

    // 第一步是将随机源向量初始化
    $iv = mcrypt_create_iv()
    // 第二步是使用PKCS7填充需要加密的字符串
    $value = static::pad($value)
    // 第三步是使用Mcrypt_encrypt加密
    $value = mcrypt_encrypt(//...)
    // 第四步是使用base64进行编码
    $value = base64_encode($iv.$value)

在解密的时候按加密相反顺序进行操作即可，但是使用了中文信息后，在移除PKCS7填充的时候我们就遇到了Unpad错误。

我们仔细分析Crypter的unpad方法：

    // 这是为了获取PKCS7填充码，Laravel使用了最简单方法获取字符串最后一个字符
	$pad = ord($value[($length = Str::length($value)) - 1]);
    // 上面的代码可以简写成这样，很明显像这样的截取字符的方法不适合于中文
    // 这是出现错误的原因之一
    $pad =  $string[$i];  

我们再来看看下面的代码：

    if (preg_match('/'.chr($pad).'{'.$pad.'}$/', $value))
    {
        // 在前面获取字符串长度的时候laravel用到了Str::length()方法
        // 但是在这里截取字符串的时候却直接使用了substr，
        // 这是出现错误的第二个原因
        return substr($value, 0, $length - $pad);
    }
    else
    {
        throw new \Exception("Decryption error. Padding is invalid.");
    }

    //Str::length()方法的代码如下
	public static function length($value)
	{
        // 我们使用中文时是使用mb_strlen来获取字符串长度的
        // 因此我们在截取字符串的时候应该是用相应的mb_substr
		return (MB_STRING) ? mb_strlen($value, static::encoding()) : strlen($value);
	}

现在找到了两个导致Decryption错误的原因，其实还有另外一个容易忽视的原因：

    // laravel/crypter.php line 67
    // Laravel在移除PKCS7填充码的时候直接使用了mcrypt_decrypt出来的字符串
    // mcrypt_decrypt会在字符串后面添加多余的编码，但这里并没有对$value进行过滤
    $value = mcrypt_decrypt(static::$cipher, $key, $value, static::$mode, $iv);
    // 这里应该使用static::unpad(rtrim($value))
    return static::unpad($value);

出现Decryption错误的原因基本已经明白了，我对Laravel框架进行了以下修改，暂时修正了这个错误：

	protected static function unpad($value)
	{
        // 删除字串末尾多余的编码
        $value = rtrim($value);

        // 获取字符串长度
        $length = Str::length($value);
        
        // MB_STRING常量在laravel/core.php中定义
        if (MB_STRING) {
            // 如果使用了宽字符集，通过mb_substr截取字符
            $pad = ord(mb_substr($value, $length - 1, 1, Config::get('application.encoding')));
        } else {
            $pad = ord(substr($value, $length - 1));
        }

		if ($pad and $pad < static::$block)
		{
			// If the correct padding is present on the string, we will remove
			// it and return the value. Otherwise, we'll throw an exception
			// as the padding appears to have been changed.
			if (preg_match('/'.chr($pad).'{'.$pad.'}$/', $value))
			{
                if (MB_STRING) {
                    // 修改substr为mb_substr，以适应中文环境
                    $value = mb_substr($value, 0, $length - $pad, Config::get('application.encoding'));
                } else {
                    $value = substr($value, 0, $length - $pad);
                }
				
                return $value;
			}

			// If the padding characters do not match the expected padding
			// for the value we'll bomb out with an exception since the
			// encrypted value seems to have been changed.
			else
			{
				throw new \Exception("Decryption error. Padding is invalid.");
			}
		}

		return $value;
    }


-- EOF --
