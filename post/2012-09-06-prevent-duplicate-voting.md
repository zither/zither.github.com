-----
{
    "title": "解决重复投票问题",
    "category": "PHP"
}
-----

<p class="meta">06 sep 2012 - By MiniMee</p>

这两天主要是在思考这个问题。通常情况下为了保证投票结果的准确性，我们都会防止用户重复投票。

在需求比较低的时候可以简单的使用Cookie或者Session来拦截电脑知识较少的人群。但是如果我们需要持久记忆投票状态的话，就必须获取用户唯一标识，然后把投票状态写入储存介质中。

我们可以写入文件，也可以写入数据库，但是这两种方式的效率都不高。我在SegmentFault上提出了这个问题，并且得到了一个比较好的解决方案：

    // 使用Redis来储存投票状态，下面是PHP演示，来自SegmentFault：
    $userId = '111';
    $questionId = '222';

    // 用户111向问题222投票，那么我们把222的userId放到名为question:vote:111的set里
    $redis->sAdd('question:vote:' . $questionId, $userId);

    // 判断222用户是否对111问题投过票了，只需判断111是否被包含在question:vote:111的set里
    $isVoted = $redis->sIsMember('question:vote:' . $questionId, $userId);

    // 我们还可以取消投票，只需要从set里移除这个元素
    $redis->sRem('question:vote:' . $questionId, $userId);
    
使用Redis的优势在于所有过程都在内存中实现，效率比数据库高很多。原帖可以查看：[PHP如何有效防止重复投票问题](http://segmentfault.com/q/1010000000116374)。

-- EOF --
