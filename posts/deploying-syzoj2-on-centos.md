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
这里还有一篇很详细的 [SYZOJ 部署指南](http://blog.masellum.me/syzoj-setup/)，是 Masellum 写的。值得参考。

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
进入 Judge 文件夹，修改 `./src/languages/` 下的 `c.ts`、`cpp.ts`、`cpp11.ts`，将 `-mx32` 替换为空。  
然后，在 Judge 文件夹下，执行 `npm run build` 重新编译评测程序。  
此时，SYZOJ2 应当已经能够正常运行。  
此外，给大家看一张图。
![准备的大坑](https://static.imvictor.tech/data/201806/syzoj_bincache.jpg)  
当时是在没有去掉 `-mx32` 的时候提交了一道题，发现无论怎么重新评测，都是 RE。试了一晚上之后还是不行，最后我暴怒删题重加，发现奇迹般的行了。  
询问 Menci 后得到的答复是，我成功跳进了 tyh 准备的大坑……  
嗯，这坑很大。
### 评测实时状态获取
在 SYZOJ2 中浏览器在获取评测状态时，会尝试 SYZOJ-Web 的 `config.json` 中定义的 `"judge_server_addr"`。如果按照官方的配置将其配置为 `http://127.0.0.1:5284`，显然用户是不能访问的。  
如果我们改成 OJ 的地址呢？在 CentOS 7 上，默认打开了防火墙，而防火墙默认会阻断 5284 端口。那么我们就有两种方案，一种是开放 5284 端口，但 SYZOJ-Frontend 监听下的 5284 端口不支持 HTTPS，导致 HTTPS 页面不能建立连接。综合来看，我们应当采用 nginx 反向代理来实现。这里提供配置文件参考。
```plain
server {
        listen 443 ssl http2;
        listen [::]:443 ssl http2;
        server_name 评测机域名;
        location / {
                proxy_set_header Host $http_host;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_pass http://127.0.0.1:5284;
                client_max_body_size 0;
        }
	#error_page 404 = /404.html; error_page 403 = /403.html;
        #ssl on;
        ssl_certificate [cert];
        ssl_certificate_key [key];
}
```
然后在 SYZOJ-Web 的 `config.json` 中将 `"judge_server_addr"` 的值修改为你设置的评测机域名。

### 其他改动
#### 服务文件修改
你需要在所有 `syzoj-*.service` 中将 `redis-server.service` 替换成 `redis.service`。  
如果你使用 MariaDB，请不要忘记在所有 `syzoj-*.service` 中将 `mysql.service` 替换成 `mariadb.service`。  
#### Web 修改
默认的，全站管理员可以在 Web 管理后台中，查看和修改配置文件。这是不安全的。因为一旦任何一个全站管理员的 Cookie 或者密码泄露，就可能导致 secret 和 token 等敏感信息被查看。  
备份一下 `[你的 SYZOJ-Web 程序目录]/views/admin_raw.ejs`，然后再建立一个新的 `admin_raw.ejs`，将以下内容写入:
```ejs
<% this.adminPage = 'raw'; %>
<% include admin_header %>
<p>系统管理员已禁止从 Web 端修改配置文件.</p>
<% include admin_footer %>
```
这样，配置文件就被限制为仅有拥有服务器 SSH 权限的人才能查看和修改的了。  
如果你的数据库选用 `utf8mb4` 编码，可能遇到 `Unhandled rejection SequelizeDatabaseError: ER_TOO_LONG_KEY: Specified key was too long; max key length is 767 bytes` 的问题，这时你应当先用 root 权限登录 mysql，然后执行:
```sql
SET GLOBAL innodb_file_per_table = ON;
SET GLOBAL innodb_large_prefix = ON;
```
然后修改 `/etc/my.cnf` 加入以下语句:
```plain
default-storage-engine=INNODB
innodb_large_prefix=on
```

----

最后放一下 OJ 链接 [山河 OJ (Mountain & River Online Judge)](https://mr.imvictor.tech/)。