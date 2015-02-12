{
    "type": "article",
    "title": "Shadowsocks client 的简单实现",
    "url": "a-simple-implemention-of-shadowsocks-client",
    "category": "Programming",
    "keywords": null,
    "date": "2015-02-12",
    "time": "15:05:02",
    "message": true,
    "publish": true
}

现在最热门的上网姿势非 Shadowsocks 莫属,当初作者发布的时候只是一个非常简单的小工具，但是没想到现在基本达到了全平台覆盖。可能有不少朋友对它的原理感兴趣，这里就让我们使用 PHP 来实现一个简单的 Shadowsocks 客户端。

首先我们需要知道 shadowsocks 在传输数据的时候是分为3个部分，这里我们使用浏览器访问网站的例子来说明：

```
+--------------------------------------------------------------+
|             1                2                 3             |
+  browser ------- ss-local ------- ss-server ------- DST host +
|           socks         shadowsocks          socks           |
+--------------------------------------------------------------+
```

从上面我们可以看出，shadowsock 将一个 socks 代理拆分为 local socks（1） 以及 server socks（3） 两个代理，这两个阶段使用 socks 协议来传递原始数据。而 local socks 与 server socks 之间（2）传输的则是经过加密的数据，我们暂且称其为 shadowsocks 协议。

在这篇文章中我们要实现的就是 ss-local，它负责接收浏览器发出的 HTTP 请求，然后将请求内容加密后转发到 ss-server。为了方便这里我们跳过了 ss-local 与浏览器 socks 握手的 method-dependent 阶段，直接纯手工构建需要传输的数据。如果你并不了解 SOCKS 协议，那么你需要先阅读 [SOCKS 协议规范](https://www.ietf.org/rfc/rfc1928.txt)。

在浏览器与 ss-local 之间的 method-dependent 协商完成后，浏览器会将需要访问的服务器地址和端口发送给 ss-local，其格式如下：

```
 +----+-----+-------+------+----------+----------+
 |VER | CMD |  RSV  | ATYP | DST.ADDR | DST.PORT |
 +----+-----+-------+------+----------+----------+
 | 1  |  1  | X'00' |  1   | Variable |    2     |
 +----+-----+-------+------+----------+----------+
```

ATYP，DST.ADDR 和 DST.PORT 这三个字段（Header）是需要通过我们的客户端转发到 ss-server 的。在我们与 ss-server 连接成功后可以完成与浏览器 socks 协商的剩下部分。浏览器再将 HTTP 请求发送到我们的客户端。我们可以直接将之前的 Header 和 HTTP 请求内容拼接在一起进行加密，然后通过之前建立的连接发送的 ss-server。加密数据的格式如下：

```
 +------+----------+----------+----------+
 | ATYP | DST.ADDR | DST.PORT |   HTTP   | 
 +------+----------+----------+----------+
 |  1   | Variable |    2     | Variable |
 +------+----------+----------+----------+
```

需要注意的是，header 部分只需要在建立通道的时候发送一次，通道建立之后直接传递加密的请求数据即可。ss-server 在收到数据后会直接解密，读取 ATYP 字段，根据其类型解析 header 部分，如果数据的整个长度超过 header 的长度，剩下的部分就会作为请求的内容。ss-server 会将从目标服务器获取的数据加密后通过通道转发回我们的客户端，我们需要将数据解密后返回给浏览器。

从整个通信过程来说，我们的客户端其实就是带有加密解密功能的 socks server。除了 socks server 最基本的功能以外，需要我们动手就是加密解密功能了。现在我们开始写代码：

```php
//source code: https://gist.github.com/zither/e35888cbb98e56f59dd4
require "Encryptor.php";
require "Cipher.php";
require "Rc4crypt.php";

$domain = "www.google.com";
$data = pack("C2", 0x03, strlen($domain));
$data .= $domain . pack("n", 0x50);
$data .= sprintf("GET / HTTP/1.1\r\nHost:%s\r\nAccept:text/html\r\n\r\n", $domain);
```

我们的客户端非常简单，简单到只支持一直加密方式：RC4。Encryptor.php，Cipher.php 和 Rc4crypt.php 这三个文件非常简单，这里只简单说明一下。Rc4crypt 提供了 RC4 加密方法的原生实现，Cipher 只是对 Rc4crypt 包裹了一下，实现了 Shadowsocks 使用的 RC4-md5 加密，其实就是每个请求使用不同的 key 而已。Encryptor 的主要作用有两个：一是通过 password 生成加密需要的 key 和 随机 iv 字符串，二是调用 Cipher 对数据进行加密和解密。

我直接跳过了与浏览器协商的部分，我们假设浏览器发出的请求是访问 Google 首页，为了摆出科学的姿势，我们不能在本地对 Domain 进行 DNS 解析，所以 Header 的内容是这样的：

```
 +------+------+----------------+----------+
 | ATYP | ALEN |    DST.ADDR    | DST.PORT | 
 +------+------+----------------+----------+
 | 0x03 | 0x0E | www.google.com |   0x50   |
 +------+------+----------------+----------+
```

上面表示的是 ATYP 为完整域名（0x03）时的格式，ALEN 表示域名的长度，DST.PORT 为端口号。SOCKS 还支持 IP4（0x01）和 IP6（0x04），对应的格式可以直接在 [SOCKS 协议规范](https://www.ietf.org/rfc/rfc1928.txt)中了解。

（未完待续）
