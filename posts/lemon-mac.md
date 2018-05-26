---
title: lemon 在 macOS 上的移植
date: 2018-04-27 23:39:20
tags: [OI,macOS,C++]
toc: true
---
lemon 是一个轻量的 OI 评测系统。它基于 Qt 编写，因此应当具有强大的跨平台特性。  
但是 2012 年开始，lemon 就不再更新了。而且之前官方也没有管 macOS 的问题，直接 qmake 也是不可能通过。  
所以，我去做了一个移植的工作。  
这里就直接贴项目地址了(其实就是骗 Star): 
* [lemon-mac 在 GitHub 上的内容](https://github.com/Chickking/lemon-mac)
* [lemon-mac 在 Coding.net 上的内容](https://coding.net/u/chickking/p/lemon-mac/)

如果你实在懒得折腾，也可以下载我构建好的版本，既可以去 GitHub Release 下，也可以[点击这里](https://raw.githubusercontent.com/Chickking-Website/SomeFile/master/201804/lemon-darwin-x86_64.zip)下载。  
![lemon 界面](https://raw.githubusercontent.com/Chickking-Website/SomeFile/master/201804/lemon01.png)  
放张图就跑~  
另外，最近中国移动网络似乎会将 GitHub 解析到 127.0.0.1。访问不了 GitHub 的朋友们就去 Coding.net 吧。
