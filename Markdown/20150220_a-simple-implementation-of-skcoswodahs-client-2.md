{
    "type": "article",
    "title": "Skcoswodahs client 的简单实现（续）",
    "url": "a-simple-implementation-of-skcoswodahs-client-2",
    "category": "Programming",
    "keywords": null,
    "date": "2015-02-20",
    "time": "15:16:07",
    "message": true,
    "publish": true
}

考虑到之前的那篇[「Skcoswodahs client 的简单实现」](/article/2015/02/12/a-simple-implementation-of-Skcoswodahs-client/)中使用了一些不恰当的第三方库，导致一些朋友在完善客户端的时候遇到了问题，特意写了这篇文章来说明一下。

在上一篇文章中，为了方便我直接使用了 [RC4Crypt](http://sourceforge.net/projects/rc4crypt/) 这个简单的 RC4 实现。但是这个库是不能真的用到客户端中去：它不支持 update 操作，每次加密或解密的时候都会重置 S box，如果连续操作的话就会出现乱码。

所以我重新了实现了一个带状态的 [RC4-PHP](https://github.com/zither/RC4-PHP) 类，用来代替之前的 RC4Crypt，使用说明如下：

```php
// Source: https://github.com/zither/RC4-PHP
require __DIR__ . "/src/RC4.php";
$password = md5("password", true);
$plaintext = "0123456789abcdefghijklmnopqrstuvwxyz";

$encryptor = new RC4($password, RC4::ENCRYPT_MODE_UPDATE);
$ciphertext = $encryptor->encrypt($plaintext);

$decryptor = new RC4($password, RC4::ENCRYPT_MODE_UPDATE);
$subCiphertext1 = substr($ciphertext, 0, 16);
$subCiphertext2 = substr($ciphertext, 16);

printf(
"------ UPDATE MODE ------\nsubPlaintext1: %s\nsubPlaintext2: %s\n",
$decryptor->decrypt($subCiphertext1),
$decryptor->decrypt($subCiphertext2)
);

// output
//------ UPDATE MODE ------
//subPlaintext1: 0123456789abcdef
//subPlaintext2: ghijklmnopqrstuvwxyz
```

为了验证可用性，我用 PHP 写了一个只支持 RC4 的 Skcoswodahs 客户端：[Psslocal.phar](http://pan.baidu.com/s/1sjuiJH7)，下载后可以得到一个 Phar 包，用 PHP 执行它即可：

```
// 客户端中使用了 mcrypt_create_iv 函数来生成 iv，运行前注意加载 MCrypt 拓展
php psslocal.phar -c ~/skcoswodahs/config.json
// 或者
sudo mv psslocal.phar /usr/local/bin/psslocal
sudo chmod u+x /usr/local/bin/psslocal
psslocal -c ~/skcoswodahs/config.json
```

这个客户端只是随手找的一个 SOCKS5 库改的，代码不敢恭维，测试即可，请勿当真。

其实用 PHP 来写 Skcoswodahs 客户端并不是一个很好的选择，抛开性能不说，在实现各种加密算法的时候都会遇到很多问题：PHP 官方自带的 Openssl 拓展不支持 context 设置和 update 操作； MCrypt 拓展各版本支持的算法不全，比如在新版本中移除了 RC4 支持。当然这些问题都可以通过安装第三方拓展来解决，但是这样就大大降低了客户端的通用性。

如果你只熟悉 PHP 语言，那么我推荐你改写 Skcoswodahs 服务端，在服务端可以使用各种高性能框架，加密算法的支持也可以通过拓展解决，那么写客户端时要面临的问题都不复存在了。
