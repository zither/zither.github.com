{
    "type": "article",
    "title": "Vagrant 与 LFS",
    "url": "vagrant-and-lfs",
    "category": "Programming",
    "keywords": null,
    "date": "2014-10-08",
    "time": "11:45:20",
    "message": true,
    "publish": true
}

这几天一直在折腾迁移之前的开发环境。我现在使用的系统只划分了 /、/home、/boot 以及 swap 这几个分区，开发环境则沿用了 Apache 的 /var/www 目录。这样带来的问题是每次系统崩溃需要重装时就必须用 live CD 进行备份数据，重装后还需要还原开发环境，我希望以后能尽量避免类似的情况。另外最近可能还有跨平台的开发需求，这也提高了保证开发环境一致性的难度，所以决定把开发环境迁移到 Vagrant 上来。

Vagrant 是一款构建虚拟开发环境的工具，可以简单的把它理解成一个集成开发环境的虚拟机。我们可以把代码目录放在宿主系统上，而代码的运行环境则由 Vagrant 来提供。Vagrant 的安装与配置非常简单，大致过程可以参照 Segmentfault 的文章：「[使用 Vagrant 打造跨平台开发环境](http://segmentfault.com/blog/fenbox/1190000000264347)」。现在我已经把自己最常用的 LNMP 开发环境通过 Vagrant 打包，然后把开发目录迁移到了 /home 分区下面，整个过程非常顺利。

在使用自己制作的 Vagrant 包的时候可能会出现：「_Vagrantfile:5: warning: already initialized constant VAGRANTFILE_API_VERSION」的问题。这是因为我们把已经配置好的 Vagrantfile 配置也打包到了里面，还原开发环境时（vagrant init）也会自己生成一个 Vagrantfile 配置文件，在这两个配置文件中重复定义了 VAGRANTFILE_API_VERSION 这个常量。从[官方文档](https://docs.vagrantup.com/v2/vagrantfile/index.html)中得知 Vagrant 使用 ruby 来解释它的配置文件，并且项目目录下的配置会覆盖 package 中的配置，所以只需要用以下方式修改 ~/.vagrant.d 中对应的 _Vagrantfile 即可：

    // 只需检测是否之前已经定义过该常量
    if !defined? VAGRANTFILE_API_VERSION
        VAGRANTFILE_API_VERSION = "2"
    end

这虽然能够修正这个错误，但是每次还原都这样修改依然很麻烦。我推荐使用这种方法修改项目目录中的 Vagrantfile，然后重新打包。

另外，我花了近 30 个小时的时间达成了成功安装 LFS 系统的成就。在迁移完开发环境之后我发现 Vagrant 包竟然有近 600M，后来在寻找更小巧的 Base 镜像时发现了一个超小的 LFS 镜像，这让我对 LFS 的兴趣大增。恰好国内恰好有完整的 LFS 安装手册，为了以后能更好的使用  linux，我决定在虚拟机里把整个安装过程跑一遍，好处多多。

这里有几点关于安装 LFS 的心得可以记录一下。如果是第一次尝试安装的话，强烈推荐下载包含完整源码的 live CD，我安装的时候直接下了一个 mini 镜像，源码都是安装时再下载，导致频繁的在宿主环境与 chroot 环境中切换，并且手册中给出的下载地址很多已失效，还需要搜索相关的 LFS mirror，非常费时。按照手册的步骤完整基本上不会出现太大的问题，我只在第六章安装 file 和 man-db 时遇到了 zlib.h 和 db.h 等头文件无法找到的问题，自己把 /usr/include 加入到 C_INCLUDE_PATH 和 CPLUS_INCLUDE_PATH 环境变量即可。

其实最可能遇到的问题应该是安装完成之后的网络设置部分，很多人都可能遇到 eth0 dosent exist 的问题。这里需要在编译 kernel 时添加相关驱动模块的支持。解决这个问题最简单的办法就是在宿主系统上使用 `lspci | grep -i Ethernet` 命令查看网卡参数，然后在 kernel 里选择合适的模块。其实究其根本，还是因为平常使用中很少会涉及到内核部分，对其众多的参数没有基本的了解。
