{
    "type": "article",
    "title": "手动更新 VirtualBox Guest Additions",
    "url": "update-virtualbox-guest-additions",
    "category": "Vagrant",
    "keywords": null,
    "date": "2014-11-02",
    "time": "10:59:22",
    "message": true,
    "publish": true
}

在使用 Vagrant 作为开发环境一段时间后，发现 Vagrant 也并不能做到完全的无缝迁移。昨天更新系统软件之后，在运行 Vagrant 时提示了 VirtualBox Guest Additions 版本不匹配的警告。这个问题一般是由 VirtualBox 更新引起的，在我们的 Vagrant 环境中，Guest Additions 版本不同最可能出现的问题就是无法挂载共享文件夹。

上面的问题可以通过升级 Vagrant box 里的 Guest Additions 来解决，升级也可以采用自动和手动两种方式。

Vagrant 的 Vbguest plugin 可以在启动时自动检测 Guest Additions 的版本，如果版本不同则自动更新同步，具体教程可以参考这篇教程：[Vagrant Tip: Sync VirtualBox Guest Additions](http://kvz.io/blog/2013/01/16/vagrant-tip-keep-virtualbox-guest-additions-in-sync/)。我测试了一下这个插件，整个更新过程基本上是不需要人为操作，但是由于国内的网络问题，可能在下载新版本 Guest Additions(60MB+) 时需要等待比较长的时间。

不过我最后还是采用了手动更新，因为使用 Vagrant 的目的是尽量不折腾，就没必要在解决由于依赖引起的问题时引入新的依赖隐患。Guest Additions 的介绍和手动更新方法在 VirtualBox 的文档 [Chapter 4. Guest Additions](https://www.virtualbox.org/manual/ch04.html) 中写得非常详细了。在这里只是把步骤做一个简单的记录，方便以后需要时查看。

```bash
# 在 Vagrant box 中安装更新依赖
vagrant@precise32:~$ sudo apt-get install linux-headers-$(uname -r) build-essential dkms
# 下载新版本的 Guest Additions
vagrant@precise32:~$ wget http://download.virtualbox.org/virtualbox/4.3.10/VBoxGuestAdditions_4.3.10.iso
# 挂载镜像
vagrant@precise32:~$ sudo mount VBoxGuestAdditions_4.3.10.iso -o loop,ro /mnt
# 卸载旧版本，可省略
vagrant@precise32:~$ sudo sh /mnt/VBoxLinuxAdditions.run uninstall
# 安装新版本
vagrant@precise32:~$ sudo sh /mnt/VBoxLinuxAdditions.run
```

手动安装的过程也非常简单，但是不论是手动还是自动安装完成之后，启动时都可能会遇到以下这两个问题：

```bash
Failed to mount folders in Linux guest. This is usually because
the "vboxsf" file system is not available. Please verify that
the guest additions are properly installed in the guest and
can work properly. The command attempted was:

mount -t vboxsf -o uid=`id -u vagrant`,gid=`getent group vagrant | cut -d: -f3` /vagrant /vagrant
mount -t vboxsf -o uid=`id -u vagrant`,gid=`id -g vagrant` /vagrant /vagrant
```

这个问题普遍反映在 Guest Additions 4.3.10 版本中，可以通过这个命令解决：

```bash
vagrant@precise32:~$ sudo ln -sv /opt/VBoxGuestAdditions-4.3.10/lib/VBoxGuestAdditions \
/usr/lib/VBoxGuestAdditions
```

另外一个问题则表现为在新版安装成功之后依然会提示版本不匹配，VirtualBox 检测到的仍然是已经卸载掉的旧版本。首先需要确认新版本已经安装成功，检测是否已经安装了 vboxguest ：

```
vagrant@precise32:~$ lsmod | grep -i vbox
vboxsf                 42523  1 
vboxguest             219244  2 vboxsf
vboxvideo              12550  0 
drm                   197692  1 vboxvideo
```

查看 vboxguest 版本：

```bash
vagrant@precise32:~$ modinfo vboxguest
filename:       /lib/modules/3.2.0-23-generic-pae/updates/dkms/vboxguest.ko
version:        4.3.10
license:        GPL
description:    Oracle VM VirtualBox Guest Additions for Linux Module
author:         Oracle Corporation
srcversion:     FDAC0A8218FE4AEFD8732E8
alias:          pci:v000080EEd0000CAFEsv00000000sd00000000bc*sc*i*
depends:        
vermagic:       3.2.0-23-generic-pae SMP mod_unload modversions 686 
```

查看 guestproperty 中的版本号（id 可以在项目目录的 .vagrant/machines/default/virtualbox/id 中查看）：

```bash
MiniThinker@localhost:~$ VBoxManage guestproperty get <id> /VirtualBox/GuestAdd/Version
Value: 4.2
```

前面确认已经成功安装新版本，VBoxManage 仍然检测到的是旧版本，手动设置更新版本号：

```bash
MiniThinker@localhost:~$ VBoxManage guestproperty set <id> /VirtualBox/GuestAdd/Version
```

在问题解决后重新打包整个环境即可，如果不怕麻烦的话，可以卸载和删掉更新过程中安装和下载的文件。
