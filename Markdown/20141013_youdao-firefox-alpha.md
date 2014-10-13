{
    "type": "article",
    "title": "Youdao firefox alpha",
    "url": "youdao-firefox-alpha",
    "category": "Programming",
    "keywords": null,
    "date": "2014-10-13",
    "time": "15:25:17",
    "message": true,
    "publish": true
}

Chromium 升级到版本 37 后在 linux 下的变现欠佳。特别是在字体方面，就算在 Chromium 中设置了相关的字体，也不能完全得到改善。不过 Firefox 32 的表现却与之相反，启动速度、浏览器界面与字体渲染都令人非常满意，所以在 Chromium 相关问题未得到解决的情况下，我会将 Firefox 作为默认浏览器。之前在 Chromium 中使用的大部分插件也有对应的 Firefox 版本，没有迁移障碍。不过翻译插件的迁移就没有那么顺利了。

之前在 Chromium 中主要使用有道词典的划词插件以及网友开发的 Halo 词典，这两者都没有对应的 Firefox 版本。Firefox 自身的翻译插件基本上都是基于 Google 翻译，以目前的网络环境来看，没翻墙的情况下差不多都已残废。我在 Firefox 插件列表中找到了一个叫 Youdao-firefox 的翻译插件，界面和快捷键还比较适合，但是试用的时候发现无法会出现无法翻译的问题，把插件解包了大致看了一下源代码，它依然使用的是 Google 翻译的接口，这是几个意思啊？

既然没能找到好用的翻译插件，那就只能自己动手了，[Youdao-firefox-alpha](https://github.com/zither/youdao-firefox) 就这样诞生了。我在 Youdao-firefox 的基础上，将 Google 翻译接口调整为有道词典的接口，删掉了仅对 Google 有用的多语言翻译配置，以及除中英以外的其他国际化支持。在界面和快捷键上仍然与原版一致，在选中单词过后按下 alt + z 就会弹出翻译框。暂时我只添加了有道翻译的基本释意，所以在翻译结果中不能看到网络释意，以后是否会支持还未决定。

插件包不准备放到官方网站上去，所以如果你也需要的话可以在 [Github](https://github.com/zither/youdao-firefox/releases) 上下载已经打包好的插件。当然，你也可以 fork 之后增加自己需要的功能，然后重新打包。
