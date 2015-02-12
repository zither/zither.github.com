{
    "type": "article",
    "title": "使用 PHP 实现简单的 SOCKS server",
    "url": "implementing-socks-server-in-php",
    "category": "Programming",
    "keywords": null,
    "date": "2015-02-10",
    "time": "13:29:03",
    "message": true,
    "publish": false
}

SOCKS 协议是一个比较好玩的协议，通过它我们可以突破一些限制访问原本无法访问的资源，就比如最近很火的 ss 代理。SOCKS 协议本身的规范很短，这里就不多做介绍，请戳这里：[RFC-1928](https://www.ietf.org/rfc/rfc1928.txt)。在这篇文章里我们将使用 PHP 来实现一个简单无验证的 SOCKS5 server。

首先我们需要监听一个本地端口来转发对应的数据，在 PHP5 之后我们可以使用 `stream_socket_*` 系列函数来实现：

```php
set_time_limit(0);

$master = stream_socket_server("tcp://127.0.0.1:5250", $errno, $errstr);
if (false === $master) {
    throw new Exception($errstr, $errno);
}

while (true) {
    $socket = @stream_socket_accept($master);
    if (false === $socket) {
        continue;
    }
    // do something
}
fclose($master);
```

在这段代码里我们创建了一个 TCP socket 对本地的 5250 端口进行监听，并在无限循环中不断检查是否有新的连接。这里我没有设置 `stream_socket_accept` 的 timeout，并抑制了超时错误提示。这样一个阻塞版的基本架子我们就搭建好了，下面我们需要实现 SOCKS server 与客户端协商的部分。

为了方便测试，我们将浏览器的代理设置为 SOCKS5，地址设置为本地监听地址。当客户端连接到我们的 SOCKS server 时会先发出一个3字节的验证请求，请求报文包括 VER，NMETHODS 以及 METHODS。VER 就是 SOCKS 版本号（version），NMETHODS 为 METHODS 的字节长度，METHODS 就是验证方法的代号。

我们写代码来看下浏览器连接 server 时发送的验证请求报文内容：

```php
while (true) {
    $socket = @stream_socket_accept($master);
    if (false === $socket) {
        continue;
    }
    $data = stream_socket_recvfrom($socket, 1500);
    if ("" === $data || false === $data || strlen($data) < 3) {
        fclose($socket);
        continue;
    }
    var_dump(unpack("CVER/CNMETHODS/CMETHODS", $data));exit;
}
```

当我们通过浏览器访问网站的时候，会看到以下结果：

```php
array(3) {
    ["VER"] => int(5)
    ["NMETHODS"] => int(1)
    ["METHODS"] => int(0)
}
```

`stream_socket_recvfrom` 收到的数据是二进制，我们 unpack 了前3个字节，看到的结果是 0x05 0x01 0x00。0x05 表明浏览器接受 SOCKS 代理的版本为5（0x05），验证方法的字节长度为1（0x01），方法代号为0（0x00）。请求报文正合我们的要求，我们可以直接回复这个请求：

```php
fwrite($socket, pack("C2", 0x05, 0x00));
```

从 SOCKS 规范中可以看到，0x00 标示不需要权限验证（X'00' NO AUTHENTICATION REQUIRED）。到这里我们完成了第一次协商，为了方便，我们要直接跳过验证请求部分。如果你需要实现验证环节，比如 Username/Password 验证，可以直接参考 [RFC 1929](https://tools.ietf.org/html/rfc1929)。


