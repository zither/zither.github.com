-----
{
    "title": "PHP生成不重复随机数字",
    "category": "PHP"
}
-----

<p class="meta">10 sep 2012 - By MiniMee</p>

在个人小项目中需要用到随机推荐,第一印象就是用PHP生成不重复的随机数字。如果我们需要生成5个数字，大多数情况可以这样做：

    $max = 50; $min = 1;
    $numbers = range($min,$max);
    shuffle($numbers);
    $lists = array_slice($numbers,0,5);

采用这种方式的好处是无论样本占总集的多少，都只需运行一次生成不会重复的随机数字，但是缺点也非常明显。这种方法的上限越大就越消耗资源，无法应用于大范围随机数。

    $max = 50;
    $numbers = array();
    while(count($numbers)<=5)
    {
        $number = rand(1, $max);
        $numbers[] = $number;
        $numbers = array_unique($numbers);
    }

我们换一种方法，直接使用rand方法来生成随机数，然后对数组进行去重。这种方法主要受限于取样的大小，如果取样太大会过多重复循环，甚至进入死循环，但是能应付大部分情况了。

上面只是讨论怎样生成随机数字，但是用上面生成的随机数据是无法用于推荐项目的。

例如我需要从数据库中随机推荐5本书籍，如果用上面的方法直接生成数字ID查询数据库，就会出现数据不存在的情况。因为随着数据库的删除增加与删除操作，表的ID不可能是连续序号。

对于这个随机推荐我暂时还没有想到很好的办法，直接使用了Mysql的随机取值：

    select * from books order by rand() limit 5;

虽然order by rand()很方便，但是必须谨慎使用，官方特意提示在记录超过300,000时你会发疯的。

**Update**:最后决定采用上面所提到的，用Rand函数生成随机数的方法来解决推荐书籍问题：

    // 储存随机推荐的书籍ID
    $books = array();

    // 获取书籍最大ID
    $max = Book::max('id');

    // 从数据库中随机取出5本书籍
    while(count($books) < 5)
    {
        $id = rand(1, $max);

        $book = Book::find($id);

        // 当随机ID对应的书籍存在并与之前所取书籍不重复，则将它出入推荐数组中
        if($book && (! in_array($book, $books)))
        {
            $books[] = $book;
        }
    }

我觉得虽然这种方法可能会出现重复查询多次的问题。但是在我的个人项目需求中，书籍数量肯定远远大于推荐数量，重复查询的问题基本可以忽略。

-- EOF --
