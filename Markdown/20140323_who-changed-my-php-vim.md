{
    "type": "article",
    "title": "谁动了我的 php.vim",
    "url": "who-changed-my-php-vim",
    "tag": "vim",
    "category": "Programming",
    "keywords": null,
    "date": "2014-03-23",
    "time": "17:59:17",
    "message": true,
    "publish": true
}

最近发生了一个非常诡异的事情，我的 vim 语法高亮竟然自动变了！在[前一篇博客](http://blog.minimee.org/article/2014/03/20/the-problem-of-vim-css-color)中我提到过：从我使用 vim 编辑器以来，color scheme 一直都是 [lucius](http://www.vim.org/scripts/script.php?script_id=2536)。它之前的模样可以在[这篇介绍](http://minimee.org/vim/)中看到。

从图片中可以看到 php 注释要比 php 普通代码的颜色要暗很多，不会吸引到视觉焦点，这也是我选择 color scheme 最重要的标准之一。但是最近它竟然自己把颜色给换了，难道注释也变得不甘寂寞了么？

<!--more-->

从[这张图片](http://ww1.sinaimg.cn/large/86e701f8gw1eepv20rot2j21ao07xdgn.jpg)可以看到，注释中部分文字改变了高亮颜色！奇怪的是最近我并没有任何更新 vim 的操作，我也不知道为什么变成了这样。在更换了多个 color scheme 之后发现，这个问题影响到了所有的 color scheme，也就是说问题与 color scheme 自身没有关系。而是 vim highlight groups 中混入了奇怪的东西。

```vimL
hi Comment          guifg=#808080           gui=none
hi Comment          ctermfg=243             cterm=none
```

这几行代码是 lucius 配色中关于注释的颜色设置，现在我需要找到混在 Comment group 中的其他 group。这时可以祭出 [SyntaxAttr.vim](http://www.vim.org/scripts/script.php?script_id=383) 这件大杀器，它的功能很简单，就是可以获取鼠标所在文字的 highlight group 信息。插件安装成功之后可以使用以下命令来获取 highlight group 信息：

```vimL
:call SyntaxAttr()
```

利用这个插件我找到了注释中高亮部分的 highlight group：

```vimL
group: phpDocTags->PreProc guifg=#efefaf(#efefaf)
group: phpDocCustomTags->Type guifg=#93e690(#93e690)
```

很显然，在 lucius scheme 之中并没有定义与这两个 group 相关的设置，关于这两个 group 的定义可以在 vim 的 syntax/php.vim 中找到：

```
syntax match  phpDocCustomTags  "@[a-zA-Z]*\(\s\+\|\n\|\r\)" containedin=phpComment
syntax region phpDocTags  start="{@\(example...\|tutorial\)" end="}" containedin=phpComment
syntax match  phpDocTags  "@\(abstract\....\|throws\)\(\s\+\)\?" containedin=phpComment
```

最后为了让这两个不安分的 highlight group 低调一点，我直接在 lucius scheme 中添加了如下两行设置：

```
hi phpDocTags       guifg=#808080                                   gui=none
hi phpDocCustomTags guifg=#808080                                   gui=none
```

然后整个 vim 又恢复到之前的宁静了。
