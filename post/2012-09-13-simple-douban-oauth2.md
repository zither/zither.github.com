-----
{
    "title": "Simple douban oauth2",
    "category": "Portfolio"
}
-----

<p class="meta">13 sep 2012 - By MiniMee</p>

这两天在思考网站头像的问题。想在自己的小项目中给用户加上小头像，所以需要考虑把头像保存在自己主机上还是直接使用现有的头像服务。

现在各大网站都提供了自己的开发API，我觉得没有必要做这些重复的工作。考虑到小项目目标人群的相关属性，Gravatar之类的国外专门头像服务肯定是不能用的，只能考虑国内几个比较大的网站，比如新浪和豆瓣。

虽然我很少使用豆瓣，但是我个人还是比较偏向使用豆瓣的API。之前没有了解过Oauth相关的信息，所以还是自己先写代码实现一下。

简单看了下豆瓣API相关的内容，使用方法很简单，大致流程如下：

    项目---->Authorization_code---->Access_token---->用户信息

想要获取豆瓣用户的信息就需要用户授权，用户同意授权之后可以得到一个Authorization_code，使用这个code就可以获取一个Access令牌，然后使用这个令牌就可以调用我们需要的信息了。

我写了一个简单的豆瓣Oauth2的类，然后下面是使用它来调用用户信息的例子：

    <?php
    /**
     * @file example.php
     * @brief 一个使用豆瓣OAUTH的简单例子
     * @author JonChou <ilorn.mc@gmail.com>
     * @version 0.1
     * @date 2012-09-13
     */

    // 包含豆瓣OAUTH类
    require('douban.php');

    // 豆瓣应用的Public Key
    $client_id = 'Your app public key';

    // 豆瓣应用的Secret Key
    $secret = 'Your app secret key';

    // 获取豆瓣授权令牌后的返回链接
    $callback = 'http://127.0.0.1/example.php';

    // 注册豆瓣OAUTH
    Douban_oauth::make($client_id, $secret, $callback);

    // 如果没有设置Authorization_code，跳转到豆瓣授权页面
    if( ! isset($_GET['code']))
    {
        // 跳转到豆瓣授权页面，获取Authorization_code
        Douban_oauth::authorization();

        exit;
    }

    // 设置Authorization_code
    Douban_oauth::$authorization_code = $_GET['code'];

    // 使用Authorization_code获取Access_token
    Douban_oauth::access();

    // 使用Access_token调用豆瓣API的例子
    // 使用豆瓣用户API获取用户信息
    Douban_oauth::user_info();

豆瓣Oauth2类和例子都上传到了Github上，有兴趣的可以看看：[Simple-douban-oauth2](https://github.com/zither/simple-douban-oauth2)。

-- EOF --
