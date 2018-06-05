---
title: ASUS Pro451L 的 High Sierra 安装报告——99% 完美
date: 2017-11-29 23:49:20
tags: [苹果,Hack,黑苹果,macOS]
toc: true
---
记得当时 OS X El Capitan 的时候，发过一篇[《【Hack】ASUS Pro451L 黑苹果 98% 完美》](https://imvictor.tech/posts/98-percent-perfection-of-ASUS-Pro451L/)。现在已经是 macOS High Sierra 了。老妈的这个本子也被我更新到了最新的系统。期间踩了不少坑，现在就把折腾的经验和全套驱动加上 EFI 文件夹 (序列号等已清除，请自行加入) 发上来。  
配置清单:
- [x] CPU: Intel Core i5-4200U 2.39GHz
- [x] 内存: 8GB DDR3 1600MHz (后期添加 4GB)
- [x] 硬盘: WDC SSD 128GB (后期更换，Trim 已开启)
- [x] 显卡: Intel HD Graphics 4400 1536MB + NVIDIA GeForce 820M 2GB (Optimus 技术独显无法使用，已屏蔽)
- [x] 声卡: Conexant SmartAudio CX20751/2 (AppleALC 驱动，麦克风不可用)
- [x] LAN 网卡: Realtek RTL8168
- [x] Wi-Fi/Bluetooth: Broadcom BCM94352HMB (更换)  
- [x] 触控板: ELAN Touchpad (10.13 触控板手势无法自定义，需配合快捷键修改使用)

完美、不完美清单:
- [x] 电池电量显示
- [x] Continuity
- [x] 亮度调节小太阳
- [x] SIP 完全开启~
- [x] App Store 系统更新
- [x] VGA 输出
- [x] iMessage、FaceTime (虽然能用但是怕被 Apple 查，不敢用)
- [ ] CX20751/2 声卡麦克风
- [ ] 触摸板手势还有一点问题
- [ ] 黑苹果哪有完美的 

其中我说到了 SIP 完全开启，这意味着系统的 kext 签名完全正常，也就是说，这和一台真正的 MacBook Pro 在系统完整性上是一致的。  


上一张运行截图:
![macOS High Sierra](https://raw.githubusercontent.com/Chickking-Website/SomeFile/master/201711/Hackintosh-Working-10.13.png)
## ACPI
在这一段，我尽量写出 Patch 的过程，方便机型相近但不同的朋友参考。   
首先，对于 NVIDIA Optimus 技术的双显卡机型，10.13 由于 Metal 2 技术的加入，对于 Optimus 双显卡的机型必须将独显屏蔽。这个是当时远景的一位热心人士帮我制作的。我后来看了一下，代码是在 SSDT-11 里面，可能应用了 RehabMan 源里的 Patch。   
10.13 还需要加入 \_DSM 模块，这个大家可以下载我的 ACPI 文件来看。   
电池的我是使用了 RehabMan 源 (本帖若无特殊说明打的补丁均来自 RehabMan 源)中的 `[bat] ASUS G75VW` Patch 到 DSDT 中。  
亮度调节在 High Sierra 中使用 DSDT。打的补丁是 `[igpu] Brightness fix (Haswell/Broadwell)`。还有 `http://github.com/RehabMan/HP-ProBook-4x30s-DSDT-Patch/raw/master` 源中的 `[7.1] Native brightness fix`。由于这个源不常见，现将 Patch 文件[附上](https://github.com/RehabMan/HP-ProBook-4x30s-DSDT-Patch/raw/2034caef887b1668a7c8fa653511cf60c164d6c6/patches/13_Brightness.txt)。  
其他补丁还有 `[sys] OS Check Fix (Windows 8)`、`[sys] Shutdown Fix v2`、`[sys] Add IMEI` 等。  
值得注意的是我并不推荐大家使用原版 MaciASL，内置的 iasl 版本不能胜任反编译的工作。因此，我编译了最新的 iasl 并制作了一个修改版的 MaciASL，[点此下载](https://raw.githubusercontent.com/Chickking-Website/SomeFile/master/201711/MaciASL.zip)。  
## 引导器配置
EFI 及相关 kext 见 GitHub 项目。SIP 完全开启，需要的请自行关闭。
项目地址: https://github.com/Chickking/ASUS-Pro451L-Hackintosh-EFI/

请自行取用。SMBIOS 等敏感信息已经去除。DSDT 等已经内置。

