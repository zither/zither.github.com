{
    "type": "article",
    "title": "Y combinator in PHP",
    "url": "Y-combinator-in-php",
    "category": "Programming",
    "keywords": null,
    "date": "2015-01-14",
    "time": "13:45:40",
    "message": true,
    "publish": true
}

```php
$Y = function($f){
    $x = function($s) use($f){
        return function($n) use($s, $f){
            return $f($s($s))->__invoke($n);
        };
    };
    return $x($x);
};

$factorial = $Y(function($recurse){
    return function($x) use($recurse){
        return $x === 0 ? 1 : $x * $recurse($x - 1);  
    };         
});

echo $factorial(6); // 720
```

花了一些时间用 PHP 推导了一遍 Y combinator，因为语法上的一些原因，推导过程中总觉得怪怪的。推导结果代码如上所示，精简和美观程度与 LISP 相差甚远，也就勉强能看。
