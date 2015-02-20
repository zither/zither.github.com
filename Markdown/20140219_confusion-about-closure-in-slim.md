{
	"type": "article",
	"title": "Slim 中关于 Closure 的问题",
	"url": "confusion-about-closure-in-slim",
	"tag": "Slim",
	"category": "PHP",
	"keywords": null,
	"date": "2014-02-19",
	"time": "15:46:17",
	"message": true,
	"publish": true
}

在翻译 Slim 框架文档的依赖注入（Dependency Injection）部分时发现一个可能会让人迷惑的地方。Slim 框架的所有变量都储存在 \Slim\Helper\Set 类的实例中，我们可以像平常一样向 Slim 中注入需要的变量。但是在注入 Closure 时就需要特别注意。这里直接使用用官方文档中的例子来说明：

<!--more-->

```php
$app = new \Slim\Slim();
$app->uuid = function(){
    return exec('uuidgen');
};
$uuid = $app->uuid;
```

在这个例子中，我们把一个匿名函数赋值给了名为 uuid 的变量，通常我们需要手动调用这个函数：

```php
$uuid = $app->uuid;
$uuid();
```

但是在 Slim 中是不需要手动调用，\Slim\Helper\Set 类会自动检查是否可调用：


```php
public function get($key, $default = null)
{
    if ($this->has($key)) {
        // 检测变量是否可以调用
        $isInvokable = is_object($this->data[$this->normalizeKey($key)]) && 
            method_exists($this->data[$this->normalizeKey($key)], '__invoke');

        return $isInvokable ? 
            // 直接调用
            $this->data[$this->normalizeKey($key)]($this) : 
            $this->data[$this->normalizeKey($key)];
    }
    return $default;
}
```

如果我们需要存储原始的匿名函数，则必须使用 \Slim\Helper\Set 的 protect 的方法，它会在我们定义的函数外面再包裹一层匿名函数（闭包）。
