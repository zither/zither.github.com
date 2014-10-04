{
    "type": "static",
    "title": "Linux 折腾记",
    "url": "linux",
    "message": false
}

![lubuntu-openbox](http://ww4.sinaimg.cn/large/86e701f8gw1ektde2c28kj21ao0t6ti8.jpg)

从 Debian 转向 Lubuntu，具体原因见[新一轮晒桌面开始](/article/2014/09/29/new-system-screenshot)。 --2014-09-29

**Nginx 502 Bad Gateway**

默认配置之下访问 web 目录可能出项「502 Bad Gateway」的错误

    // 需要修改 nginx 的 www.conf
    // No such file or directory 错误，在 listen = 127.0.0.1:9000 后增加下列
    listen = /var/run/php5-fpm.sock
    // Permissoion denied 错误，需启用以下设置
    listen.owner = www-data
    listen.group = www-data
    listen.mode = 0660

**Lubuntu 下为 openbox 右键菜单增加应用列表**

新装 Lubuntu 之后，在 openbox 环境下右键菜单没有应用列表，解决办法如下：
    
    // 安装 openbox-xdgmenu
    sudo apt-get install openbox-xdgmenu
    // 修改 openbox root menu，新增如下菜单：
    <menu id="xdg-apps" label="Applications" execute="openbox-xdgmenu /etc/xdg/menus/lxde-applications.menu" />

**rxvt-unicode**

运行 rxvt-unicode 之后出现「urxvt: unable to parse color , using pink instead.」问题。需要修改 /usr/sbin/lightdm-session 文件，去掉 xrdb 载入 .Xresources 时的 -nocpp 参数

**替换 nm-applet 图标**

Lubuntu 的 network-admin 使用了 nm-applet 来显示 system tray，在 openbox 桌面下，它会默认选择 gnome 或者 hicolor 中的相关图标，与美化的图标不协调，需要手工替换 nm 对应的几个图标。

![Debian sid](http://ww4.sinaimg.cn/large/86e701f8gw1ed1iq7ljhij21ao0t6qem.jpg)

自上次调整系统之后又将近半年时间了，由于采取的是惰性升级，所以系统一直稳定至今，只是调整了下桌面挂件。 --2014-01-30

![Debian sid](http://ww4.sinaimg.cn/large/86e701f8jw1e6rdiahpqsj21ao0t67g8.jpg)

用了一段时间的 Debian stable 之后继续升级到 sid 版了，原因无他，我想要用中州韵。在 stable 版下尝试自己编译，但是依赖库实在都太新了，基本无法解决。所以又得再次晒桌面了。--2013-07-18

![Debian sid](http://ww4.sinaimg.cn/large/86e701f8jw1e4nrvvbe7lj21ao0t6tc0.jpg)

距离上次装系统已经将近一年了，前两天升级Debian后就无法进入x-window，尝试各种方法无果后选择重新安装。记录果然是个好习惯，参考自己的wiki一路安装和设置都非常顺利，上面就是新系统的桌面啦。

**Openbox环境下的音量控制**

在Openbox环境下控制音量并不是很方便，习惯终端的朋友可以直接使用alsamixer来进行调节。为了方便仍然推荐使用一款叫做obmixer的音量控制小软件，可以使用obmixer改名后的pnmixer，后者功能更多一些。

    // obmixer下载地址
    http://jpegserv.com/obmixer/

**Obmixer 和 Pnmixer 自动启动问题**

发现在Openbox环境中，obmixer 或者 pnmixer 经常会出现无法自动启动的问题，尝试了各种配置均无效，最后发现只要加大obmixer 或者 pnmxier 的延迟启动时间就很少出问题了，我设定的是15秒。

![Debian sid](http://ww4.sinaimg.cn/large/86e701f8jw1dz7jyyf6t7j.jpg)

经过老爷机这段时间的使用，已经喜欢上了之前搭配的轻量级桌面环境，所以决定把之前的ubuntu环境也更换掉。这个文档记录了Debian sid的整个硬盘安装过程(非顺序记录)，以备以后查看。

**Debian sid 安装**

电脑之前就已经安装有Windows xp，因此直接通过硬盘安装Debian sid。我使用的是电信4M宽带，带自动拨号，就直接选择了网络安装的方式。这里碰到一个问题就是官方源中直接提供的sid版netboot installer都会出现找不到内核模块的问题，比如这里：[us.debian.org](http://http.us.debian.org/debian/dists/sid/main/installer-i386/current/images/netboot/)。google无果之后选择先安装test然后升级到sid。

    // 我选择了daily版，下载地址如下
    http://d-i.debian.org/daily-images/i386/daily/netboot/

直接下载链接中的mini.iso镜像和debian-installer/i386目录中的initrd.gz和linux三个文件放置到xp的c盘根目录。

下载Grub4dos0.44版，把其中的grldr复制到c盘根目录，然后打开c盘根目录下的boot.ini(系统文件)，在末尾添加：

    c:\grldr="Grub4dos"

关闭保存，然后在根目录新建一个menu.list文件，内容如下

    title Debian installer
    root (hd0,0)
    kernel /linux
    initrd /initrd.gz

重启后即可进入Debian的安装界面。由于是通过网络安装，所以在选择源的时候要选择一个比较快的，我直接使用了163的源。Debian的安装过程非常清晰，为了方便配置系统，在选装软件时我勾选了安装桌面环境，如果你有信心直接从framebuffer配置出桌面环境就不用安装了。

在等待不到两个小时之后Debian就安装成功了，默认安装了gnome-shell桌面，感觉和unity差不多，不是很喜欢。现在直接升级到sid版。

    // 修改apt源
    $ sudo vi /etc/apt/sources.list
    // 把wheezy换成sid
    deb http://mirrors.163.com/debian/ sid main contrib non-free
    deb-src http://mirrors.163.com/debian/ sid main contrib non-free

    deb http://mirrors.sohu.com/debian/ sid main contrib non-free
    deb-src http://mirrors.sohu.com/debian/ sid main contrib non-free
    // 更新源
    $ sudo aptitude update
    // 版本升级
    $ sudo aptitude dist-upgrade

到现在为Debian sid的基本系统已经安装完成，剩下的就可以进行我的DIY工程了。

**N卡闭源驱动安装**

系统安装完毕之后，默认安装的是Nouveau开源驱动，各种特效都可以支持，但是CPU风扇转得很快，电脑CPU为Pentium Dual-Core E5400,显卡Geforce 310。看来开源驱动还是有问题，为了解决风扇问题，需要更换为闭源驱动。

直接到NVIDIA官方网站下载合适自己显卡的linux驱动，比如我下载的是NVIDIA-Linux-x86-304.64.run，安装过程可以参照这篇文章：[DebianLenny 安装NVIDIA显卡驱动](http://linux.blogbus.com/logs/35468874.html)

    // 进入root，需输入密码
    $ su
    // 安装依赖
    $ aptitude install build-essential linux-headers-$(uname -r)
    // 把驱动放置到/usr/src
    $ cp NVIDIA-Linux-x86-304.64.run /usr/src
    // 联接
    $ ln -s linux-headers-$(uname -r) linux
    // 关闭X-window
    $ /etc/init.d/gdm3 stop
    // 按ctrl+alt+F1进入tty模式
    // 安装显卡驱动
    $ cd /usr/src && sh NVIDIA-Linux-x86-304.64.run

在安装显卡驱动过程中会出现一些选项，选择接受许可，不从网站下载内核。我在安装过程中还提示gcc版本不符，我的gcc要新一些，所以就直接选择了编译，最后要同意NVIDIA自动更新xconfig配置文件。

    // 驱动安装完毕之后直接启动X
    $ startx
    // 运行NVIDIA X-Server Settings
    $ nvidia-settings
    // 选择X Server Display Configuration选项，在菜单中选择Save to X Configuration File

安装完显卡驱动之后CPU风扇终于安静了。

**安装openbox**

我的目标是配置一个轻量级的桌面环境，最终选择的软件为：Openbox tint2 wbar feh thunar rxvt-unicode。

    // 安装环境软件
    $ sudo aptitude install openbox tint2 wbar feh thunar rxvt-unicode
    // 用feh设置壁纸
    $ feh --bg-scale /path/to/wallpaper.file
    // 创建并设置openbox自动启动软件
    $ gvim ~/.config/openbox/autostart
    // 内容如下 
    eval `cat ~/.fehbg` &
    (sleep 1 && tint2) &
    (sleep 1 && wbar -pos bottom -above-desk -bpress &) 

现在openbox环境的基本样子已经搭起来了，但是都是采用的默认设置，比较难看,并且wbar还会出现斑块，这是因为openbox默认不支持透明的缘故。大多数情况下我们都推荐使用xcompmgr来提供渲染。

    // 安装xcompmgr
    $ sudo aptitude install xcompmgr
    // 修改openbox的启动文件
    $ gvim ~/.config/openbox/autostart
    // 添加如下内容
    xcompmgr -c -C -t-5 -l-5 -r4.2 -o.55 &

重新启动openbox之后我们可以看到wbar和tint2已经支持透明了，并且程序边框还带有阴影，如果不喜欢阴影可以直接去掉xcompmgr后面的参数。这里我又遇到一个问题，在xcompmgr启用阴影之后我的终端(rxvt-unicode或者gnome-terminal)在拖动时都会出现残影，非常难看，在尝试各种参数设置无果后只能放弃xcompmgr转而使用compton。

**配置终端**

gnome-shell默认使用的是gnome-terminal作为终端，它在使用起来也比较方便，可以可视化配置各种参数。不过由于它使用了gtk-3.0，在openbox下不太方便美化，所以直接使用了rxvt-unicode。前面已经安装了rxvt-unicode终端，初次运行时它比gnome-terminal要难看无数倍，有点对不住观众，不过经过我们配置之后它会变得漂亮起来。

    // 创建并编辑urxvt的配置文件
    $ gvim ~/.Xresources

在这个配置文件里可以配置urxvt的各种参数，由于参数比较多，我直接上传到了这里：[.Xresources](https://github.com/zither/config/blob/master/.Xresources)。英文字体采用了Consolas，中文直接使用了微软雅黑，然后背景颜色和壁纸相搭配，采用90%的透明度。

在编辑完配置文件之后我们的urxvt终于可以入眼了。

**配置tint2**

在panel方面我选择了比较简洁的tint2，安装方便，配置更加方便，并且官方有非常详细的wiki。我基本上完全使用了官方的默认配置，只是简单的修改了下位置、字体及字体大小，全部配置可以在这里查看：[tint2rc](https://github.com/zither/config/blob/master/tint2rc)。

**配置compton**

由于我一直没有解决xcompmgr启用阴影效果后终端出现残影的问题，所以只能放弃使用xcompmgr，转而使用compton。compton是从xcompmgr fork后的修改版本，并且一直到现在都还在更新。

    // compton下载地址
    https://github.com/chjj/compton

Debian sid源里并没有提供现成的包，所以我只能从github上clone一份源码，然后编译安装。compton的文档还是很详细的，README中直接写明了所有编译和运行的依赖，因此只需要逐个安装，然后make即可。

compton也可以使用配置文件来修改渲染效果，比起使用参数更加直观和容易修改。同时官方也给出了一个完整的示例，只需要修改参数值就可以配置让自己满意的效果了。

    // 我使用得配置文件
    https://github.com/zither/config/blob/master/.compton.conf
    // openbox中使用compton
    compton --config ~/.compton.conf &

**LAMP 环境的安装**

总体来说Debian sid下的PHP环境搭建是非常简单的，基本只需一路aptitude进行安装即可。

    // 安装apahce2
    $ sudo aptitude install apache2
    // 安装PHP5
    $ sudo aptitude install php5
    // 安装PHP5-mcrypt拓展
    $ sudo aptitude install php5-mcrypt
    // 安装PHP5-mysql拓展
    $ sudo aptitude install php5-mysql
    // 安装php5-curl拓展
    $ sudo aptitude install php5-curl
    // 安装Mysql
    $ sudo aptitude install mysql-server-5.5
    // 启用apache rewrite
    $ sudo a2enmod rewrite 
    $ sudo service apahce2 restart

**细节备注**

Debian默认浏览器为Iceweasel(冰鼬)，不过我还是喜欢chromium多一些，这里碰到一个问题就是如果使用chromium自带的标题栏和边框，在进行最大化操作时就会出现显示区域刷新问题，必须等待一段时间才能完全显示。解决方法就是在chromium外观设置中勾选“使用系统标题栏和边框”即可。

-- EOF --
