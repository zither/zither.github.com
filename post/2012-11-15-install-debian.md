-----
{
    "title": "Debian安装记录",
    "category": "Linux"
}
-----

<p class="meta">15 Nov 2012 - By MiniMee</p>

经过老爷机这段时间的使用，已经喜欢上了之前搭配的轻量级桌面环境，所以决定把之前的ubuntu环境也更换掉。这篇文章记录整个安装过程(非顺序记录)，以备以后查看。

**LAMP 环境的安装**

总体来说Debian sid下的PHP环境搭建是非常简单的，基本只需一路aptitude进行安装即可。

    // 安装apahce2
    $ sudo aptitude install apache2
    // 安装PHP5
    $ sudo aptitude install php5
    // 安装PHP的Mcrypt拓展
    $ sudo aptitude install php5-mcrypt
    // 安装PHP的Mysql拓展
    $ sudo aptitude install php5-mysql
    // 安装Mysql
    $ sudo aptitude install mysql-server-5.5
    // 启用apache rewrite
    $ sudo a2enmod rewrite 
    $ sudo service apahce2 restart

**Debian sid 安装**

电脑之前就已经安装有Windows xp，因此直接通过硬盘安装Debian sid。我使用的是电信4M宽带，带自动拨号，就直接选择了网络安装的方式。这里碰到一个问题就是官方源中直接提供的sid版netboot installer都会出现找不到内核模块的问题，比如这里：[us.debian.org](http://http.us.debian.org/debian/dists/sid/main/installer-i386/current/images/netboot/)。google无果之后选择先安装test然后升级到sid。

    // 我选择了daily版，下载地址如下
    http://d-i.debian.org/daily-images/i386/daily/netboot/

直接下载链接中的mini.iso镜像和debian-installer/i386目录中的initrd.gz和linux三个文件放置到xp的c盘根目录。

下载Grub4dos0.44版，把其中的grldr复制到c盘根目录，然后打开c盘根目录下的boot.ini(系统文件)，在末尾添加：

    c:\grldr="Grub4dos"

关闭保存，然后在根目录新建一个menu.list文件，内容如下

    default 2
    timeout 5

    title Debian installer
    root (hd0,0)
    kernel /linux
    initrd /initrd.gz

重启后即可进入Debian的安装界面。由于是通过网络安装，所以在选择源的时候要选择一个比较快的，我直接使用了163的源。Debian的安装过程非常清晰，为了方便配置系统，在选装软件时我勾选了安装桌面环境，如果你有信心直接从framebuffer配置出桌面环境就不用安装了。

在等待不到两个小时之后Debian就安装成功了，默认安装了gnome-shell桌面，感觉和unity差不多，不是很喜欢。

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
