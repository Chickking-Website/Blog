---
layout: post
title: FRP 的配置和使用
date: 2018-07-16 22:00:27
tags: [Linux,网络,网站]
toc: true
---

最近发现经常出现 502 Bad Gateway，这让我很不爽，山河 OJ 刚刚火了不久，怎么就闹出这种事情来。  
后来发现是 ZeroTier One 的锅，于是在 Menci 的建议下，采用 FRP 转发流量到家里的服务器。现在稍作记录，以备今后我自己查看和他人配置需要。

### 双边配置
首先执行 `wget https://github.com/fatedier/frp/releases/download/v0.20.0/frp_0.20.0_linux_amd64.tar.gz`，下载 frp。并解压。这里我们假定你的 frp 程序在 `/opt/frp/` 路径。

### 服务端
首先服务器必须是有公网 IP 的。
修改 frps.ini 或者 frps_full.ini，这两个模板根据自己的情况修改，full 的那个更模板全一点。我的配置文件为 frps_full.ini。  
下面详解一下常用各项的意思:
- bind_port 绑定的交流端口，和客户端一致即可。
- bind_udp_port 打洞端口，没被占用、和 bind_port 不一致即可。
- kcp_bind_port KCP 协议端口，可以和 bind_port 一致。
- token 保证客户端和服务端一致，切勿泄露。
- tcp_mux TCP 连接复用，建议设置为 `true`。

在 `/etc/systemd/system/` 下创建 `frps.service`，内容请根据自己的实际情况修改:
```
[Unit]
Description=frps

[Service]
Type=simple
WorkingDirectory=/opt/frp
ExecStart=/opt/frp/frps -c /opt/frp/frps_full.ini
StandardOutput=null
Restart=on-failure

[Install]
WantedBy=multi-user.target
```
**请注意，frps 应当使用 root 用户启动。因为，在类 Unix 系统中，非 root 帐户不能绑定 1000 以下的端口。**  
然后执行 `sudo systemctl enable frps.service && sudo systemctl start frps.service`。

### 客户端
修改 frpc.ini 或者 frpc_full.ini，这两个模板根据自己的情况修改，full 的那个更模板全一点。我的配置文件为 frpc_full.ini。  
下面详解一下常用各项的意思:
- server_addr 服务器地址，域名或 IP 都可。
- server_port 与服务器的 bind_port 一致即可。
- token 验证，与服务器的 token 一致即可。
- tcp_mux 与服务器端效果相同
- protocol 协议，默认 TCP。

下面添加配置块，配置块的基本格式如下:
```
[备注]
type = 连接类型 (tcp / kcp)
local_ip = 本地 IP (一般填写 127.0.0.1)
local_port = 本地端口
remote_port = 远程端口
```
例如:
```
[https]
type = tcp
local_ip = 127.0.0.1
local_port = 443
remote_port = 443
```
在 `/etc/systemd/system/` 下创建 `frpc.service`，内容请根据自己的实际情况修改:
```
[Unit]
Description=frpc

[Service]
Type=simple
WorkingDirectory=/opt/frp
ExecStart=/opt/frp/frpc -c /opt/frp/frpc_full.ini
StandardOutput=null
Restart=on-failure

[Install]
WantedBy=multi-user.target
```
然后执行 `sudo systemctl enable frpc.service && sudo systemctl start frpc.service`。  
这样，FRP 基本上就能充当一个合格的前端代理了。
