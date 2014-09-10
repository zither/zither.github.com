{
    "type": "article",
    "title": "重写了 Mreader",
    "url": "new-version-of-mreader",
    "tag": "Programming",
    "category": "Mreader",
    "keywords": null,
    "date": "2014-09-10",
    "time": "17:44:01",
    "message": true,
    "publish": true
}

[Mreader](http://mreader.sinaapp.com) 是我自己写的一个 Feed 聚合工具，第一个版本诞生于 2013 年 5 月。当时写 Mreader 的原因是 Google reader 即将关闭，虽然那段时间出现了非常多的 Google reader 替代品，但是由于稳定性的原因没有使用它们。后来就花了几天时间自己写了一个简陋的 Web 聚合，然后一直使用到 [Digg reader](http://digg.com/reader) 的发布。

Digg reader 算是 Google reader 的最佳替代品，我也一直使用到现在。不过最近国类又开始封锁国外的各种服务，在没有出墙的情况下，Digg reader 的良好体验被大大的降低了。然后我又一次想到了已经被搁置很久的 Mreader，决定花两天时间把它重写了一遍。基本的订阅功能今天已经完成了，前端方面基本直接 Copy 了我另一个小站的样式。

现在还有「星标」功能没有完成，我认为这是 Feed 聚合最重要的功能。Google reader 受到那么多人的喜欢，很大程度是因为能通过它找到很多相关网站已经消失的文章。所以我也打算在 Mreader 上永久保存我认为值得收藏的一些博客文章。这需要考虑到图片防盗链的问题，我准备只把「星标」文章的图片抓取到本地进行保存，方便以后查看。
