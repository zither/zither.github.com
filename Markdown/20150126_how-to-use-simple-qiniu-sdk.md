{
    "type": "article",
    "title": "Simple Qiniu SDK 使用说明",
    "url": "how-to-use-simple-qiniu-sdk",
    "category": "Programming",
    "keywords": null,
    "date": "2015-01-26",
    "time": "11:06:51",
    "message": true,
    "publish": true
}

为了解决[「手绘本」](http://www.shouhuiben.com)的头像托管问题，前几天重写了一遍 [Simple Qiniu SDK](https://github.com/zither/simple-qiniu-sdk)，剔除了上传以外的其他功能。这是吸取了之前的教训：只维护自己用得到的代码。因此现在的 Simple Qiniu SDK 只能用于小文件上传。下面是一个简单的使用示例：

```php
// Autoload 源码：https://github.com/zither/simple-qiniu-sdk/blob/master/example/Autoload.php
require __DIR__ . "/Autoload.php";
Autoload::addNamespace('Qiniu', dirname(__DIR__) . '/src/Qiniu');
Autoload::register();

$accessKey = 'accessKey';
$secretKey = 'secretKey';
$qiniu = new \Qiniu\Qiniu($accessKey, $secretKey);

$bucket = $qiniu->getBucket('sketch');
$response = $bucket->put($_FILES['file']['tmp_name']);
echo $response->getContent();
```

这次重写我剔除了 Simple Qiniu SDK 中的自动加载函数，所以在使用前你需要自己解决自动加载问题（推荐使用 Composer）。这个示例使用的是默认设置，如果你需要修改七牛的上传策略，可以使用 setPolicy 方法：

```php
$bucket = $qiuniu->getBucket('sketch');
// 更多策略参数请参考：http://developer.qiniu.com/docs/v6/api/reference/security/put-policy.html
$bucket->setPolicy(array(
    'saveKey' => sprintf("%s.jpg", time()),
    'returnBody' => '{"key": $(key),"name": $(fname)}',
    'expires' => 3600
));
```

你不仅可以指定文件保存的名称，还可以设置 \Qiniu\Bucket::EXTR_OVERWRITE 参数来启用 put（更新） 模式：

```php
$bucket->put($_FILES['file']['tmp_name'], 'avatar.png', \Qiniu\Bucket::EXTR_OVERWRITE);
```

你可以自定义一些魔术变量，然后以数组的形式传递给 put 方法：

```php
$bucket->setPolicy(array(
    // 通过 returnBody 的形式返回魔术变量
    'returnBody' => '{"key": $(key), "user": $(x:user)}',                  
));
$uploadParams = array(
    // 文件保存名称
    'key' => 'avatar.png',
    // 自定义魔术变量
    'x:user' => 'Simple Qiniu SDK'
);
$bucket->put($_FILES['file']['tmp_name'], $uploadParams);
```

如果你希望采用[表单上传模式](http://developer.qiniu.com/docs/v6/api/overview/up/form-upload.html)，你可以使用 getUpToken 方法来获取上传令牌：

```html
<form action="http://upload.qiniu.com/" method="post" enctype="multipart/form-data">
    <input name="file" type="file" />
    <input name="x:user" type="hidden" value="Simple Qiniu SDK">
    <input name="token" type="hidden" value="<?=$bucket->getUpToken()?>">
    <button id="upload" type="submit">上传到七牛</button>
</form>
```

以上就是重构后 Simple Qiniu SDK 所有功能的使用说明，如果你还需要「删除文件」等其他功能，可以自行修改或者使用[官方 SDK](http://developer.qiniu.com/docs/v6/sdk/php-sdk.html)。当然，如果我也有了对应的使用需求，也会给 Simple Qiniu SDK 增加对应的功能支持。 
