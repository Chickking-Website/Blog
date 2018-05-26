---
title: 打造自己的随身 Ubuntu
date: 2018-1-13 19:55:20
tags: [Linux, Ubuntu]
toc: true
---
如今真的是 Windows 满天飞啊，虽然说好了不黑 Windows 了，但是那个系统实在让我感到难受。（cmd.exe 的锅占一半）

虽然大部分时间都可以用 Mac，但是还是有可能会用到外面的电脑。如果不想用 Windows 怎么办？随身携带一个操作系统是非常好的解决方案。

显然，我们不能携带 macOS。因为 Mac 还没有普及到满地都是，而 Hackintosh 需要针对每台机器进行不同的配置，完全不适合携带。那么我们只能选择 Linux 阵营。在 Linux 中，权衡各方面利弊，我选择了 Ubuntu 17.10 artful.

先晒晒截图。
![Dash](https://github.com/Chickking-Website/SomeFile/raw/master/201801/ubuntu/2018-01-13%2019-45-28%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)
![Chrome](https://github.com/Chickking-Website/SomeFile/raw/master/201801/ubuntu/2018-01-13%2019-46-18%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)
![Integration](https://github.com/Chickking-Website/SomeFile/blob/master/201801/ubuntu/2018-01-13%2019-53-27%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png?raw=true)

1月14日更新，Ubuntu 已经通过 Docky 使用 Dock。
![Dock](https://github.com/Chickking-Website/SomeFile/blob/master/201801/ubuntu/2018-01-14%2012-59-55%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png?raw=true)

## 环境准备
建议有一个可用的 macOS 环境，以便安装 Clover EFI Tools。  
一个 USB 磁盘，建议 USB 3.0 + 64GB。  
Ubuntu 17.10 artful.  
## 启动
我们首先需要将 Ubuntu 安装到 U 盘中。安装过程建议使用虚拟机，并采取 EFI + GPT 的方式。  
安装完成后，你会发现 Ubuntu 可能不能正常启动，此时需要先安装 grub。
grub 的安装非常简单，这里不再赘述。网上这样的教程一大把一大把的。  
重点是，如果遇上不支持 EFI 的电脑怎么办？  
折腾过黑苹果的同学可能知道，Clover 有一个安装方式是 EFI Tools。其中，EFI Tools 提供了安装模拟 UEFI 环境的功能。  
EFI Tools 也比较好找，例如:链接: https://pan.baidu.com/s/1dFTprdJ 密码: 76gm。版本虽然有些老，但是我们的重点是里面模拟 UEFI 的 boot0ss。  
在 macOS 启动 EFI Tools 并选择 “G、挂载 EFI 分区到桌面不格式化(Fat32)”，然后“H、(一)安装 Clover EFI 开机引导文件(Fat32)”，注意要挂载的是 U 盘的 EFI 分区。
将启动文件写入后，在终端中执行以下命令。
```bash
# 这里假设您的 EFI 分区在 /Volumes/EFI 挂载
dir="/Volumes/EFI"
mkdir $dir/EFI/BOOT
rm -rf $dir/EFI/CLOVER/*
cp $dir/EFI/ubuntu/grubx64.efi $dir/EFI/BOOT/BOOTX64.EFI
cp $dir/EFI/ubuntu/grubx64.efi $dir/EFI/CLOVER/CLOVERX64.EFI
```
这样，U盘就能够在不支持 EFI 的电脑上启动了。  
我们可以在 Ubuntu 的 `/etc/grub.d/40_custom` 中加入一些自定义代码，来扩充 grub 的菜单项。我的如下：
```bash
#!/bin/sh
exec tail -n +3 $0
menuentry '退出 Grub' {
        exit
}
menuentry 'ACPI 关闭计算机' {
        halt
}
menuentry '重新启动计算机' {
        reboot
}
# This file provides an easy way to add custom menu entries.  Simply type the
# menu entries you want to add after this comment.  Be careful not to change
# the 'exec tail' line above.
```
## 桌面美化
从第三张截图可以看到，尽管 Unity 在 Ubuntu 17.10 artful. 中已经被抛弃，我还是更愿意使用它。因为 Unity 对全局菜单支持的很好。这是我非常喜欢的 Mac 的功能之一，在 Ubuntu 上自然不可或缺。  
主题使用 OSX-Arc-Darker，图标使用 Ultra-Flat。  
字体是我这个 Ubuntu 的特别之处，使用苹方、San Francisco 和 SF Mono，而不是 Ubuntu 默认字体。  
**<span style="color: red">注意： 由于苹方、San Francisco、SF Mono 字体的版权归 Apple Inc. 所有，我不确定是否有潜在的法律风险，故不提供下载。请自行去 macOS 中提取使用。</span>**  
SF Mono 路径： `/Applications/Xcode.app/Contents/SharedFrameworks/DVTKit.framework/Versions/A/Resources/Fonts`。  
San Francisco + 苹方都在 `/Library/Fonts/` 下。  
通过 `unity-tweak-tool`  加载使用。该工具还可以调节很多东西。 
## 常用软件
我在 macOS 上日常会使用的软件一般包括 Safari、oh-my-zsh、Sublime Text、Moeditor、搜狗拼音、QQ、微信、网易云音乐等。    
*1月14日更新，现在文档软件使用的是 WPS Office。*  
浏览器方面，Safari 肯定不行了。Ubuntu 自带了 Firefox 浏览器，如果你喜欢当然很好，但是 Ubuntu 自带的 Firefox 实在不怎么样。因此我们要替换浏览器。  
我建议直接安装 Chrome。  
oh-my-zsh 也是非常有用的 zsh 扩展，详情可以到  https://ohmyz.sh/ 查看。  
Sublime Text、Moeditor、搜狗拼音也可以直接下载安装。  
QQ 这个东西不太好办，我最终选择安装一个 VirtualBox 虚拟机，系统采用 《Deepin.XP.SP3.Lite.完美精简版.iso》占据空间很小，分配 8GB 的空间足矣。
微信的话，有一个第三方封装的项目——[electronic-wechat](https://github.com/geeeeeeeeek/electronic-wechat)。我替它做了一个图标和 Desktop 文件。图标在下面提供，直接保存即可。
![WeChat icon](https://github.com/Chickking-Website/SomeFile/blob/master/201801/ubuntu/wechat_logo/icon.png?raw=true)
Desktop 文件请将如下代码保存到 `/usr/share/applications/wechat.desktop`。其中请将 `#YOUR DOWNLOADED DIR#` 替换成你保存这个项目 release 文件夹的路径，`#ICON PATH#` 替换成你下载的图标的路径。
```plain
[Desktop Entry]
Name=WeChat
GenericName=WeChat
Comment=WeChat built with electron
Exec=#YOUR DOWNLOADED DIR#/electronic-wechat
Icon=#ICON PATH#
Terminal=false
Type=Application
Categories=Network;
StartupNotify=true
```
网易云音乐在 Ubuntu 17.10 artful 上可能无法使用，是因为依赖包的问题。[这篇文章](https://www.jianshu.com/p/6f0747db7018) 提供了解决方案。
WPS Office 是比较符合我们使用习惯的。有一个问题是它在高分屏下的界面有点问题，不过功能是正常的。在安装时，可能会遇到依赖问题。  
要解决 WPS Office 的依赖问题，首先要将它的依赖包装齐：
```bash
sudo apt-get install libc6 libfreetype6 libcups2 libglib2.0-0 libpng12-0 libglu1-mesa libsm6 libxrender1 libfontconfig1 libxext6 libxcb1
```
你可能会发现 `libpng12-0` 不能正常安装。这样的话我们就只能去 Ubuntu 16.04 的源安装：
```
wget http://kr.archive.ubuntu.com/ubuntu/pool/main/libp/libpng/libpng12-0_1.2.54-1ubuntu1_amd64.deb
sudo dpkg -i libpng12-0_1.2.54-1ubuntu1_amd64.deb
```
然后去 [WPS Community](http://wps-community.org/download.html) 下载最新的 `wps-office_10.1.0.5707~a21_amd64.deb` 安装即可。  
另外 ShadowsocksR 在 Ubuntu 上的配置也比较难找，我在下一篇博文中详细叙述了配置方法。

