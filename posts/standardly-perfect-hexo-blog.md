---
title: Hexo 博客程序初步完善
date: 2017-11-04 16:09:20
tags: [网站]
toc: true
---
切换到 Hexo 之后，首要的任务就是把之前的一些轮子再造一遍。  
## 1. 版权声明和原文链接
在旧的博客中，我曾经加入过这样的功能:
![版权声明和原文链接](https://raw.githubusercontent.com/Chickking-Website/SomeFile/master/201711/sourcemark.png)
在 emlog 中，这些非常容易做。而在 Hexo 中，这也不是难事。  
我使用的是 yilia 主题，在主题的 `layout/_partial/article.ejs` 中，在 `<% post.content %>` 之后加入以下代码:
```
<% if (config.copyrights && page.layout == "post") { %>
	<hr>
	<p>本文著作权归作者所有，允许非商业性转载使用，但请在转载后文末附上原文链接。</p>
	<p>原文链接: <a href="https://<%= config.domain %><%= post.permalink %>">https://<%= config.domain %><%= post.permalink %></a>，<a href="https://<%= config.domain %><%= post.permalink %>#gitalk-container">前往原文评论。</a></p>
<% } %>
```
前往原文评论部分 URL 的 Hash 值请根据你使用的评论系统的 Container ID 修改。
然后在 Hexo 的 `_config.yml` 文件中加入如下配置:
```yaml
domain: imvictor.tech # 你的域名
copyrights: true
```
这样可以灵活控制是否打开这个功能，也方便切换域名。
## 2. 查看 Markdown 源文件
这个灵感最初是来自 imququ.com，再次致敬。  
在 emlog 中，这个功能实现起来反而更加复杂，因为 emlog 本身不支持 Markdown，我是通过自己的方式来实现 Markdown 支持的。在 emlog 中，为了输出 Markdown 源文件，我使用了一些比较 trick 的方法，就不说了。  
现在，我几乎没有动 Hexo 就实现了这个功能，因为 nginx 可以很好地完成这个工作。  
先对 `source/_posts/` 进行符号链接到 `source/posts/`。   
在 nginx 的 Config 文件中加入以下代码:
```
location / {
    root /home/user/hexo/public/;
	location ~* \.(md)$ {
		root /home/user/hexo/source/;
    }
}
```
这样在请求 Markdown 文件时会自动到 source 下寻找。  
e.g.:
```dontparse
Query: https://imvictor.tech/posts/simulation-of-ppc-macos-via-qemu.md

Output File: source/posts/simulation-of-ppc-macos-via-qemu.md
```
可以根据个人情况修改。  
在主题的 `layout/_partial/article.ejs` 中，在 `<% post.content %>` 之后适当位置加入以下代码:
```
<% if (config.mdsource && page.layout == "post") { %>
    <div style="margin-right: 1em; text-align: right;"><a href="/posts/<%= post.slug %>.md">查看本文 Markdown 版本 »</a></div>
<% } %>
```
在 Hexo 的 `_config.yml` 中加入 `mdsource: true`。  
这样就完成了 Hexo 的查看 Markdown 源文件。

## 3. Hexo 的部署自动化
由于我们使用 CentOS 服务器，所有的操作都在服务器上完成。  
首先我希望在 Git Server 上备份我的 Hexo 博客源文件，也希望能在上面部署一份。这些根据很多人的教程都可以做。我还希望服务器每隔 10 分钟重新生成静态文件。这样，我们可以编写一个脚本。
```bash
#!/bin/bash
HEXOPATH="# 你的 Hexo 文件夹绝对路径，末尾不带 /"
cd $HEXOPATH
hexo g
git add $HEXOPATH/* && git commit -m "Backup at $(date)" && git push -u origin master
hexo deploy
exit 0
```
在 crontab 中，加入如下代码:
```cron
*/10 * * * * ~/hexogenerate.sh > /dev/null
```
这样服务器就会每隔 10 分钟重建一次静态文件。

还有一些比如 GitHub Issue 评论系统的部署，这些网上教程也是一大把。我在这里只强调一点: 注意在`<meta>`头里面加入 AJAX 跨域访问的值项，也没什么坑。