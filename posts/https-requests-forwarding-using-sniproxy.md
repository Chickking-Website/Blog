---
layout: post
title: SNIProxy 实现服务器反向代理多个带加密网站
date: 2018-08-18 16:04:27
tags: [Linux,网络,网站]
toc: true
---
之前通过 FRP 来实现了单个网站不泄露证书的 HTTPS 反向代理，但是缺点是一台服务器只能代理一个网站，即一个前端只能对应一个后端。这多少有些不够方便，也不方便与他人合租一台服务器。

SNIProxy 则是一个不错的解决方案。它通过浏览器访问时提供的 SNI 头来判断访问域名，然后可以根据域名来选择转发请求到何处。由于这个过程不涉及加密和解密，因此除了匹配正则表达式几乎没有性能损失，同时也不需要在前端服务器上部署 HTTPS 证书，也无需担心数据泄露的问题。  
本教程适用的 Linux 发行版为 CentOS 7.4。

## 安装 SNIProxy
SNIProxy 这个软件并不在大多数 Linux 操作系统默认的软件源中。因此需要手动编译以及安装。
编译安装教程可以参考软件官方 README.md。项目的地址是: [SNIProxy on GitHub](https://github.com/dlundquist/sniproxy)。  
我这里也提供一个打包好的 RPM 包，可直接通过 `yum` 安装在 CentOS 7 上。下载地址为 [Cloud Storage](https://cloud.imvictor.tech/s/b8WqDWsJcrbgfLQ)。  
## 配置 SNIProxy
安装好包之后，SNIProxy 应该已经在 `/usr/sbin/sniproxy` 了。  
在大多数情况下，我们希望它作为一个服务启动，这样方便管理。但 SNIProxy 并没有把自己安装为服务，因此需要我们手动添加。  
将以下内容放置到 `/etc/systemd/system/sniproxy.service`。
```ini
[Unit]
Description=SNIProxy

[Service]
Type=simple
ExecStart=/usr/sbin/sniproxy -f
StandardOutput=null
Restart=on-failure

[Install]
WantedBy=multi-user.target
```
这样就可以通过 `systemctl` 来控制 SNIProxy 了。  
然后我们需要编辑 SNIProxy 的配置文件 `/etc/sniproxy.conf`，配置文件示例参见官方 README。 

下面有几点小提示:
1. 注意，`listener` 后面如果带有 `127.0.0.1`，则只有本机才能传入连接。因此，一般推荐把 `listener 127.0.0.1:443`，改为 `listener 443`。 
2. `fallback` 参数的意思类似于 switch-case 语句的 `default` 选项。即所有给出的情况全部失配后，选择将请求转发到这个参数所配置的服务器上。
3. SNIProxy 也可以转发 HTTP 流量，只要把 `protocol` 后面的 `tls` 改成 `http` 即可。

## 其它
对于 FRP 端口映射前端服务器，建议开启防火墙，保证所有流量都能经过 SNIProxy。