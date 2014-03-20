{
    "type": "article",
    "title": "vim-css-color 严重降低 vim 启动速度",
    "url": "the-problem-of-vim-css-color",
    "tag": "VIM",
    "category": "Programming",
    "keywords": null,
    "date": "2014-03-20",
    "time": "12:11:09",
    "message": true,
    "publish": true
}

因为一直使用 vim-gtk 的缘故，我其实很少使用 vim 进行编程，color scheme 更是一直使用 [lucius](http://www.vim.org/scripts/script.php?script_id=2536)。不过最近有些工作必须要在终端下完成，为了改善在 vim 中编程的体验，我还是决定升级下终端版本，并专门把 vim 的配色更换为 Tomorrow-Night。

我使用的终端是 rxvt-unicode，默认为88色，而 Tomorrow-Night 的 256 色要比 88 色看起来舒服很多，所以我直接把 rxvt-unicode 换成了 rxvt-unicode-256color。没想到在终端更换完毕之后出现了一个问题：用 vim 打开文件的速度变得非常慢，最少需要4-5秒左右。

<!--more-->

使用如下命令来查看 vim 的启动过程耗费的时间：

```php
vim --startuptime timefile test.php
```

这个命令会打开 test.php 文件并在当前目录生成一个名为 timefile 的启动日志文件。然后我在日志文件中找到了拖慢 vim 启动速度的真凶：

```php
191.477  004.767  004.767: sourcing /usr/share/vim/vim74/syntax/vb.vim
4279.165  4087.443  4087.443: sourcing /home/chou/.vim/bundle/css_color.vim/syntax/css.vim
```

从日志中可以看到，大部分时间都被用于加载这个名为 css_color.vim 的插件。话说这个插件比较好玩，特别是对于经常接触前端的朋友，它可以自动修改文件中表示各种颜色格式文字（比如 #ffffff）的背景，然后让人可以直观的看到这些数字组成的颜色。我当即在 .vimrc 配置中去掉了这个插件，然后 vim 的启动速度恢复正常。

随手 google 了一下，发现非常多的人在 [github issue](https://github.com/skammer/vim-css-color/issues/3) 上提出了这个问题，但是作者未作任何回复。大致是由于插件的 rgb2xterm 函数效率过低。不过有好心的网友自己对这个问题进行了更新修复，更新后的插件可以在这里找到：[https://github.com/ap/vim-css-color](https://github.com/ap/vim-css-color)。

之前在 88 色终端下并未发现这个问题是由于插件在开头添加了这样一行环境检测代码：

```vimL
if !( has('gui_running') || &t_Co==256 ) | finish | endif
```

PS：我顺手安装测试了一下网友修正过的插件，启动速度很快，推荐使用。
