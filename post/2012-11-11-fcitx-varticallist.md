-----
{
    "title": "修改fcitx候选词为横排显示",
    "category": "Linux"
}
-----

<p class="meta">11 Nov 2012 - By MiniMee</p>

上一篇Debian折腾记还没写完网络光纤就意外断裂，这种状态一直持续到昨天才恢复。Linux这个分类主要是记录我使用Linux系统的过程，因为接触linux系统不久，所以难免会记录一些比较幼稚的问题。

今天要写的是关于fcitx输入法候选词列表的修改。Debian sid默认使用的是fcitx输入法，候选词列表是竖排显示的，但是最新版的fcitx的配置工具暂时还没有提供候选词显示方式选择，旧版本反而有。

所以干脆翻了一下fcitx输入法的配置文件，发现切换功能其实还在，只是在配置工具中没有提供。这里记录下修改过程：

    // fcitx的个人配置都保存在这里
    $ cd ~/.config/fcitx/conf
    $ gvim fcitx-classic-ui.config
    // 修改VerticalList属性
    VerticalList=False
    // 重启fcitx(fcitx重启快捷键为ctrl+5)
    pkill fcitx && fcitx -d

-- EOF --
