-----
{
    "title": "Redirecting with data",
    "category": "Laravel"
}
-----

<p class="meta">05 sep 2012 - By MiniMee</p>

通常当我们在处理完一些请求后会需要跳转到某个页面，并显示一些信息。

举个例子，当用户提交登录信息之后，我们要对信息进行检测，然后跳转到相关页面：

    Route::post('login',function()
            {
                // 检测登录信息是否正确
                if( Input::get('username') != 'minimee')
                {
                    // 登录失败，跳转会登录页面
                    return Redirect::to('login');
                }
            });

在上面的代码中，当登录失败后会重新跳转会登录页面，但是我们没有对用户做任何提示。这样的跳转非常不友好，我们需要给用户一些有用的信息。

在使用Redirect类的时候我们可能会遇到两种情况需要返回错误信息，第一种：

    Route::post('login',function()
            {
                // 检测登录信息是否正确
                if( Input::get('username') != 'minimee')
                {
                    // 登录失败，跳转会登录页面
                    return Redirect::to('login')->with('error','用户名不正确');
                }
            });

    // 在视图中我们可以这样显示信息
    @if(Session::has('error'))
        <div id="error">{{ Session::get('error') }}</div>
    @endif

当我们使用Validator类的时候可以这样传递错误信息:

    Route::post('register',function()
            {
                $input = Input::get('username');

                $rule = array('username' => 'required');

                // 使用Validator类进行检测
                $validation = Validator::make($input,$rule);

                if($validation->fails())
                {
                    // 未通过检测，返回注册页面
                    return Redirect::to('auth/register')
                                // 同时返回错误信息
                                ->with_errors($validation->errors);
                }
            });

    // 在视图中我们可以这样显示错误信息
    @if($errors->has())
        <div id="error">{{ $errors->first() }}</div>
    @endif

这里有一个地方需要我们注意，就是Validator类的回馈信息都保存在**application/language/**目录下，默认情况是**application/language/en/validation.php**。

你可以看到语言目录中没有中文，当然我们可以自己建立一个cn目录，然后把validation.php中的信息翻译成中文。

但是当你使用中文信息，并且Session.driver使用的是Cookie的时候程序是无法正常运行的，你会看到一个encrypt错误。

我暂时没有时间去深究这个错误出现的根源，初步猜测是使用非英文字符的时候超出了cookie长度限制，临时解决办法有两个：

    // 使用Validator类的$message参数
    $input = Input::get('username');
    $rule = array('username' => 'required');
    $message = array('required' => '你必须填写 :attribute 。');

    // 使用Validator类进行检测
    $validation = Validator::make($input,$rule,$message);

    // 又或者你可以修改Session的driver选项
    // application/config/session.php line 19	
    'driver' => 'file',

-- EOF --
