{
    "type": "article",
    "title": "Memo View Component",
    "url": "how-to-use-memo-view-component",
    "category": "Memo",
    "keywords": null,
    "date": "2015-03-06",
    "time": "14:55:07",
    "message": true,
    "publish": true
}

最近在对自写的 Memo 框架做一些结构调整，准备将它的一部分组件独立出来以便分开使用，最先完成的是 [Memo View Component](https://github.com/zither/Memo)。Memo View 是一个轻量级的使用 PHP 原生语法的模板“引擎”，你可以使用它来实现简单的模板继承功能。

Memo View 使用 PHP 原生语法，所以我们可以这样定义一个 base 模板：

```php
// file templates/base.php
<html>
    <head>
        <meta charset="utf-8">
        <?=$this->section('title')?>
    </head>
    <body>
        <?=$this->section('content')?>
    </body>
</html>
```

base 模板中的 section 方法相当于一个占位符，具体内容需要子模板来填充。我们再定义一个基于 base 的 index 子模板：

```php
// file templates/index.php
<?php $this->layout('base'); ?>

<?php $this->open('title'); ?>
    <title>Memo Framework</title>
<?php $this->close(); ?>

<?php $this->open('content'); ?>
    <h1>Memo</h1>
    <p>Memo is a PHP micro framework.</p>
    <?=$this->section('github')?>
<?php $this->close(); ?>
```

在 index 模板中使用 layout 方法来声明其继承于 base 模板。然后使用 open 和 close 方法来填充对应 section 的内容。Memo View 支持 layout 的嵌套，所以我们在 index 模板中同样可以使用 section 方法。

然后我们可以使用 Memo View 来载入之前定义的模板：

```php
// file index.php
require __DIR__ . "/Memo/src/Memo/View.php";

use Memo\View;

try {
    $view = new View(array(
        "template" => "index",
        "folders" => array(__DIR__ . "/templates/")
    ));
    $view->display();
} catch (Exception $e) {
    echo $e->getMessage(), PHP_EOL;
}
```

Memo View 的 constructor 可以接受一个数组作为初始化参数，`template` 是模板名，`folders` 是模板目录。`folders` 可以同时定义多个目录，载入时会按顺序查找，并使用最先找到的模板。输出的内容大致如下：

```html
<html>
    <head>
        <meta charset="utf-8">
        <title>Memo Framework</title>
    </head>
    <body>
        <h1>Memo</h1>
        <p>Memo is a PHP micro framework.</p>
    </body>
</html>
```

在模板中使用变量也非常方便，我们继续定义一个基于 index 的子模板 github：

```php
// file templates/github.php
<?php $this->layout('index'); ?>

<?php $this->open('title'); ?>
    <title>Fork me on Github</title>
<?php $this->close(); ?>

<?php $this->open('github'); ?>
    <p>Github: <a href="<?=$repo?>"><?=$this->toUpper($repo)?></a></p>
<?php $this->close(); ?>
```

在 github 模板中又设置了一次 title secion 的内容，并且在 github section 中使用了 $repo 变量 以及 toUpper 方法。

```php
// file index.php
require __DIR__ . "/Memo/src/Memo/View.php";

use Memo\View;

class Helper 
{
    public function toUpper($string)
    {
        return strtoupper($string);
    }
}

try {
    $view = new View(array(
        "template" => "github",
        "helper" => new Helper(),
        "folders" => array(__DIR__ . "/templates/")
    ));
    $view->assign("repo", "https://github.com/zither/Memo");
    // 可以通过 $view->render() 方法来获取内容
    echo $view->render();
} catch (Exception $e) {
    echo $e->getMessage(), PHP_EOL;
}
```

上面的代码中我们新增了一个 `helper` 参数，其值为 Helper 类的对象，我们需要通过它来提供 github 模板中需要的 toUpper 函数。然后使用 assign 方法设置了 repo 变量。输出内容大致如下：

```html
<html>
    <head>
        <meta charset="utf-8">
        <title>Fork me on Github</title>
    </head>
    <body>
        <h1>Memo</h1>
        <p>Memo is a PHP micro framework.</p>
        <p>Github: <a href="https://github.com/zither/Memo">HTTPS://GITHUB.COM/ZITHER/MEMO</a></p>
    </body>
</html>
```

从输出的内容可以看到，github 模板中定义的 title 覆盖了 index 模板中设置的内容，这点需要注意。同时 index 模板中定义的 github section 也被正确填充，这说明 Helper 中的 toUpper 方法也被正确调用。
