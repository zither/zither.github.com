-----
{
    "title": "ubuntu替换SunPinyin默认图表",
    "category": "Linux"
}
-----

<p class="meta">14 Oct 2012 - By MiniMee</p>

Linux平台下的中文输入法没有windows平台那么多选择。习惯五笔的人对输入法的要求要相对低一些，但是拼音输入法就大不一样。ubuntu系统默认的拼音输入法使用起来体验不是很好，更新之后就一直使用SunPinyin，但是不得不吐嘈的是SunPinyin的默认图标实在是难看。

顺手google了一下，发现有网友为SunPinyin绘制了一个新图标，在官方博客上作者也推荐了这款图标，并给出了Mac的替换方法，有兴趣的可以看下这里：[SunPinyin的新图标](http://yongsun.me/2009/03/sunpinyin%E7%9A%84%E6%96%B0%E5%9B%BE%E6%A0%87/)。新图标虽然不是Linux风格，但是比默认的要好看很多，于是替换之。

这里记录下Ubuntu12.4的Ibus框架替换过程。其实和上面文章中的过程下不多，只要替换掉输入法里对应的图标即可。

- 首先当然一样是[下载](http://chengis.me/fruity/wp-content/uploads/icons/sunpinyin-icon.zip)图标文件,并解压缩。
- 然后在替换掉Ibus-sunpinyin目录下的图标，具体命令如下。

    sudo cp SunPY-03.png /usr/share/ibus-sunpinyin/icons
    cd /usr/share/ibus-sunpinyin/icons
    sudo mv sunpinyin-logo.png sunpinyin-logo-backup.png
    sudo mv SunPY-03.png sunpinyin-logo.png

替换之后使用输入法时图标看起来就要舒服得多了。

-- EOF --
