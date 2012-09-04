-----
{
    "title": "关于MiniList",
    "category": "PHP"
}
-----

<p class="meta">30 Aug 2012 - By MiniMee</p>

这两天想写一个非常简单的Todolist，只需要一个PHP文件和一个txt文件。整个程序只有一个功能，就是从txt文件中读取事件，然后生成一个多级嵌套列表。txt文件数据很简单，只是一行一行的文字：

    // minilist.txt
    Project MiniList
        实现最基本的读取和展示
        改进算法
    C语言学习
        Beginning C
    Laravel
        中文文档翻译计划

事件以"\n"为分隔符，事件之间的亲子关系则以空格缩进为依据。最开始的时候花了几分钟写了一个最基础的Demo：

    // minilist.php
    $mlist = array();
    $data = rtrim(file_get_contents('minilist.txt'));
    $lists = explode("\n",$data);

    $regex = '/^\s{4}.+/';
    foreach($lists as $list){
        $num = preg_match($regex,$list);
        if(!$num){
            $mlist[] = array('type'=>0,'value'=>$list);

        }else{
            $mlist[] = array('type'=>1,'value'=>$list);
        }
        }

    $output = '<html lang="zh-CN">
        <meta http-equiv="content-type" content="text/html; charset=utf-8" />
        <h1>MiniList</h1>
        <ul>';

    $n = count($mlist);
    for($i = 0; $i < $n; $i++){
        if($mlist[$i]['type'] == 0 && $mlist[$i+1]['type'] == 1){
            $output .= "<li>{$mlist[$i]['value']}";
        }else if($mlist[$i]['type']==0){
            $output .= "<li>{$mlist[$i]['value']}</li>";
        }else if($mlist[$i]['type'] == 1 && $mlist[$i+1]['type'] == 1){
            $output .= "<ul><li>{$mlist[$i]['value']}</li></ul>";
        }else{
            $output .= "<ul><li>{$mlist[$i]['value']}</li></ul></li>";
        }
    }

    $output .= '</ul></html>';
    echo $output;

我知道这个几分钟时间拼凑出来的Demo写得很难看，不过它可以表达出我想要的是什么样的效果。

今天重新看了下Demo，觉得只要重写一下列表生成部分的代码就大致可以用了，但是悲剧的是我没有想到什么特别好的办法来解决循环嵌套问题。

你可以看到Demo中用了一堆难看的if-else判断语句，这还是只有二级嵌套，如果多级嵌套的话，我想不出代码有多难看。

暂时只想到一个方法，用数组模拟数据库，每个事件都是一个数组，并且每个数组都有一个parent值来表明亲子关系，例如：

    array('1' => array('parent' => 1,'value' => value))

在生成嵌套的时候只迭代parent为0的根事件，这样可以避免使用累赘的判断，然后在根事件中递归迭代子事件。不过我觉得这样虽然比Demo中好看一点，但是仍然不简洁。

在PHP版Markdown中有现成的嵌套列表生成方法，等自己想不到更好的办法时再去看吧。

代码已经重写，可以查看文章：[用PHP生成嵌套列表](http://blog.minimee.org/2012/08/31/a-simple-todo-list.html)

--EOF--
