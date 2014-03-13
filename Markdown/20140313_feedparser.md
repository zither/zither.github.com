{
    "type": "article",
    "title": "Feed 解析那些库",
    "url": "feedparser",
    "tag": "PHP",
    "category": "Programming",
    "keywords": null,
    "date": "2014-03-13",
    "time": "10:26:53",
    "message": true,
    "publish": true
}

前几天做了一个比较小的项目，大部分功能都涉及到 feed 解析。关于 feed 解析我之前也写过相关的代码，因为所需 feed 的情况都在自己的控制之内，当时使用的是自己写的一个解析类。这次项目要求要更加通用一些，所以在进行过程搜索了各种开源的 feed 解析库。

最先关注到的解析库是 SimplePie，它的功能比较齐全，基本上支持全格式的 feed 解析，还包括了转码缓存等功能。不过在解析中文订阅的时候会出现将中文编码为 HTML Entities 的问题。随手 google 了一下，发现有人已经遇到过相同的问题：[SimplePie 将中文编码成 HTML Entities 的解决办法](http://blog.webfuns.net/archives/1710.html)。

文中已经说明出现这个问题的原因，并且给出了一个解决办法，不过修改方法不太清楚，这里详细说一下：

    // 修改的内容在 SimplePie_Sanitize 类的 sanitize 方法中
    class SimplePie_Sanitieze
    {
        // 忽略无关的内容
        // ...
        // ...

        public function sanitize($data, $type, $base = '')
        {
            // 忽略无关内容
            // ...
            // ...

            if ($type & (SIMPLEPIE_CONSTRUCT_HTML | SIMPLEPIE_CONSTRUCT_XHTML))
            {
                $document = new DOMDocument();
                $document->encoding = 'UTF-8';

                // 增加一下两行代码
                $unique_tag = '#' . uniqid() . '#';
                $data = $unique_tag . $data . $unique_tag;
                
                $data = $this->preprocess($data, $type);
                // 继续忽略无关内容
                // ...
                // ...

                // 直接注释下面这两行代码
                //$real_body = $document->getElementsByTagName('body')->item(0)->childNodes->item(0);
                //$document->replaceChild($real_body, $document->firstChild);

                $data = trim($document->saveHTML());

                // 最后在添加下面这行代码
                list($_, $data, $_) = explode($unique_tag, $data);

                // ...
                // ...
            }
        }
    }

除了 SimplePie 之外还在 Github 上找到一个非常小的解析库：[FeedParser](https://github.com/dzeban/FeedParser)。这个库的功能相比 SimplePie 要简陋太多，不过好在它足够简单，源代码看完想怎么改就怎么改。FeedParser 的作者也是因为自己需要而写的，最后更新在 3 年之前，提供的接口是使用 XPath 硬查询的，所以如果有特殊的需求需要自己修改代码。我自己使用的时候也添加过一些简单的接口，代码放在：

    // FeedParser Github repo
    https://github.com/dzeban/FeedParser
    // 我小小修改过的
    https://github.com/zither/FeedParser

总的来说，FeedParser 适合自己玩，正规一些的项目可以考虑使用 SimplePie。
