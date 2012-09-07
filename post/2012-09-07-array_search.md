-----
{
    "title": "array_search的小陷阱",
    "category": "PHP"
}
-----

<p class="meta">07 sep 2012 - By MiniMee</p>

在PHP的数组函数中我们可以看到这样一个函数：

    mixed array_search ( mixed $needle , array $haystack [, bool $strict ] )

array_search可以让我们在一个数组中搜索是否含有某一个值，比如：

    $array = array(0 => 'blue',1 => 'red');
    // $key = 1
    $key = array_search('red',$array);

这个函数给我们提供了非常好用的数组搜索功能，当在数组中找到指定值的时候会返回该值的键值。

但是我们必须小心的使用这个函数，因为它的返回值可能会给我们带来一些问题，我们在下面的代码：

    $framework = 'laravel';
    
    $frameworks = array(0 => 'laravel',1 => 'cakephp',2 => 'codeigniter');

    if(array_search($framework,$frameworks)){
        echo 'Got it';
    }
    else{
        echo 'Sorry.';
    }

通常情况下我们习惯用if(true){//..}这样的判断方式。

上面的代码中我们想确认已有的框架数组是否包含laravel框架，从代码上看我们预期的结果是Got it，但实际上我们看到的是Sorry。

frameworks数组中包含有laravel这个值，但是我们却得到了意外的答案，这就是我们需要注意到地方。

在PHP5中array_search在没有找到指定值时会返回FALSE，找到的情况下会返回键值。我们可以看到上面的代码中laravel值的键值是0，因此我们查找laravel的返回是0。

这个0会给我们带来困扰，上面代码可以简化成这样:

    if(0){
        echo 'Got it.';
    }else{
        echo 'Sorry.';
    }

程序不会知道这个0到底是false还是键值0，在上面的情况中它会被认为是false。我们需要更加准备的判断返回值：

    $framework = 'laravel';
    
    $frameworks = array(0 => 'laravel',1 => 'cakephp',2 => 'codeigniter');

    if(false !== array_search($framework,$frameworks)){
        echo 'Got it';
    }
    else{
        echo 'Sorry.';
    }

-- EOF --
