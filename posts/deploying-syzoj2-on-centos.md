---
title: SYZOJ2 在 CentOS 上的搭建
date: 2018-06-14 23:12:20
tags: [OI,Linux]
toc: true
---
这一个月倒是干了不少事，只是没找到时机记录下来。  
前不久高爷带我去捡垃圾，弄到一台主机当服务器。CPU 是 Pentium(R) Dual-Core E5700 @ 3.00GHz，想到比 BZOJ 还好一点，于是我就想搭建一个 OJ。  
由于我对于 SYZOJ2 比较习惯，所以选择 SYZOJ2。  
但是，SYZOJ2 官方只提供了 Ubuntu 的教程，在 CentOS 上，有些东西会不一样。  
本文需要对照官方安装指南查看，详情请阅读 [syzoj/syzoj on GitHub](https://github.com/syzoj/syzoj/) 和 [Demo 服务器账号及搭建指南 - 帖子 - Demo](https://syzoj-demo.t123yh.xyz:20170/article/1)。

### SYZOJ-Web
Web 的搭建相对比较简单。大部分都可以按照 SYZOJ2 官方的教程来做。  
这里只说明不一样的地方。  
对于在 Ubuntu 下的这段命令:
```
apt install build-essential libboost1.58-all-dev nodejs rabbitmq-server redis-server nginx p7zip-full
```
在 CentOS 上，首先，不能通过 yum 源安装 nodejs，因为 yum 源中的 nodejs 太古老，需要安装 8.0 版本(可能低一点也可以)。安装脚本如下:
```bash
curl --silent --location https://rpm.nodesource.com/setup_8.x | sudo bash -
```
nginx 需要单独加源，方法自行搜索，很多。  
剩下的包则用接下来的命令安装:
```
yum groupinstall "Development Tools"
yum install boost-devel rabbitmq-server redis p7zip p7zip-plugins
```
其他步骤可以参照官方教程来进行。  
另外，不清楚是不是 bug，如果在 config.json 中，将 `"hostname"` 字段设置为一个域名(这样注册验证电子邮件的地址就是这个域名)，那么必须在 `/etc/hosts` 中将这个域名的 IP 地址解析到 127.0.0.1，否则会无法启动 Web 端。

### SYZOJ-Judge
Judge 端的情况，相对有一些问题。在 CentOS 上，出现了严重的问题，就是 C++ 程序默认是会 RE 的。经过我 chroot 到沙盒排查，这个问题是 `-mx32` 编译参数导致的。所以我们需要修改这个编译参数。  
进入 Judge 文件夹，修改 `./src/languages/` 下的 `c.ts`、`cpp.ts`、`cpp11.ts`，将 `-mx32` 替换为空即可。  
此时，SYZOJ2 应当已经能够正常运行。  
此外，给大家看一张图。
![准备的大坑](https://static.imvictor.tech/data/201806/syzoj_bincache.jpg)  
当时是在没有去掉 `-mx32` 的时候提交了一道题，发现无论怎么重新评测，都是 RE。试了一晚上之后还是不行，最后我暴怒删题重加，发现奇迹般的行了。  
询问 Menci 后得到的答复是，我成功跳进了 tyh 准备的大坑……  
嗯，这坑很大。

----

最后放一下 OJ 链接 [山河 OJ (Mountain & River Online Judge)](https://mr.imvictor.tech/)。