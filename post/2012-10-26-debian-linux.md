-----
{
    "title": "Debian折腾记",
    "category": "Linux"
}
-----

<p class="meta">26 Oct 2012 - By MiniMee</p>

前面说过关于系统环境的迁移，前段时间刚刚迁移到Linux时一直使用的是对新手比较友好的Ubuntu。到现在已经非常习惯在Linux下进行各项活动。一时兴起想把家里那台10年老爷机也换为linux系统。

老爷机的配置已经落后这个时代多年了，intel图拉丁1G的CPU，256sdr内存。老爷机之前一直运行在盗版xp下作为小朋友的学习机使用，虽然年纪比较大，但是运行稳定。

对于这样低配置的老爷机来说，Ubuntu连想都不用想，因此两款对配置要求很低的linux系统被列入了选单。首先是最有好感的Archlinux，强大的wiki支持，滚动升级，最小模式安装，各种符合老爷机的要求。Archlinux系统的安装比较繁琐，特别是7月15版取消了自动安装脚本之后。

在老爷机上重装了不下10遍之后我承认败给了Archlinux，因为不管我怎么设置编码，装完之后都是乱码。这还不是平常所说的中文乱码，而是连英文到数字全是白色的斑块，Google无果之后果断放弃，投奔Debian。

首先晒一下配置完成之后的效果图，一张桌面，一张终端和Gvim。

<img src="http://pic.yupoo.com/ilorn/CmVGcjqo/13IZDc.png" width="100%" />

<img src="http://pic.yupoo.com/ilorn/CmVGduPB/Y25P9.png" width="100%" />

最开始安装的是Debian stable，但是源里的软件大多过于陈旧，随后重新安装Debian sid。由于一开始就是奔着轻量级去的，登录管理器采用了Slim，桌面使用的是Openbox。为了美观和体验，还额外安装了Tint2和Wbar，壁纸由feh绘制，xcompmgr优化透明。

体验了一段时间之后发觉非常贴合我的个人习惯，再回到ubuntu的unity反倒是觉得别扭了。终端选择了自带的gnome terminal，不知道为什么使用起来速度比uRxvt要快。

-- EOF --
