{
    "type": "article",
    "title": "新一轮晒桌面开始",
    "url": "new-system-screenshot",
    "category": "Linux",
    "keywords": null,
    "date": "2014-09-29",
    "time": "15:26:06",
    "message": true,
    "publish": true
}

![openbox](http://ww4.sinaimg.cn/large/86e701f8gw1ektde2c28kj21ao0t6ti8.jpg)

据可靠日期记录，我持续使用了近一年半的 Debian sid 系统终于被我滚挂了。事情的起因要归结到这两天被大家玩坏了的 bash 漏洞，一直采用超惰性升级的我，在为这个漏洞打补丁的时候顺手滚动升级了一下系统软件，然后就没有然后了。这次崩溃非常彻底，以前出问题最多是无法启动 x-window，这次连 /sbin/init 都无法启动了，提示说 segment fault。期间尝试了很多种修复方案，但未能见效。

无奈之下只好重新安装 Debian，采用一贯的硬盘启动网络安装的方式，过程非常顺利。未料到的是在我辛苦调教好 openbox，重启之后崩溃再现，仍然无法启动 /sbin/init 进程。令人疑惑的是我在新系统上没有做任何危险操作，只是安装了 openbox，docky 等几个我常用的软件而已。继续重装，然后这个过程重复了 4 次，我尝试了 Debian testing 和 sid 两个版本以及几个常用的桌面环境，都无一例外的在重新安装 openbox 环境重启之后崩溃。

因为手里没有 Linux 的系统盘，很多检查工具用不了，就直接尝试安装其他的 Linux 发行版了。考虑到使用习惯，选择了自带 openbox 的 lubuntu 发行版，图片中就是最后的调教效果，已经非常接近之前的样式了，只是换了一下壁纸。其实 lubuntu 自带的主题也挺好看的（如下图），但是与 conky 有兼容性问题。

<!--more-->

![lubuntu](http://ww3.sinaimg.cn/large/86e701f8gw1ektdcl5te6j21ao0t6k0q.jpg)

这张图就是 lubuntu 默认主题，只是换了一下图标而已。另外需要说到的就是开源的显卡驱动进步了很多，没有出现之前风扇狂转的问题了，但是在登录之后仍然有花屏的现象。暂时先这样用着，过段时间再考虑需不需要换到闭源驱动。
