---
title: 秋天的一波新 API 上线
date: 2018-09-17 17:03:03
tags: [php, API]
toc: true
---
这几个月添加了一些 API。现介绍如下:
## 一言
### API 名称
Hitokoto
### API 说明
每次推荐一条一言。

### URL
https://api.imvictor.tech/php-api/v7/hitokoto  

### HTTP 请求方式
GET
### 请求参数
#### `charset` 可选
选择文本编码，可选的编码有 UTF-8 和 GBK。若不传入该参数，则默认为 UTF-8。
### 请求示例  
- Request URL: https://api.imvictor.tech/php-api/v7/hitokoto   
- Request Method: GET  

### 返回数据示例
- Status Code: HTTP/2.0 200

```plain
无论你在这世上的什么地方，我都一定会再去见你的
```
### 其他
“一言” 有另一个特别版本，称为[“唐言”](https://github.com/SDLYYZ/Tangokoto)。该项目首先被用于 [Mountain & River Online Judge](https://mr.imvictor.tech)。现在该项目由 [Mountain & River Online Judge](https://mr.imvictor.tech) 和 [LYOI Online Judge](https://lyoi.ac) 共同维护。  
关于此 API 的使用，请参阅[该项目的 GitHub 主页](https://github.com/SDLYYZ/Tangokoto)。

## 网易云音乐
### API 名称
Cloudmusic
### API 说明
本 API 内含 3 个子 API，分别是获取直链、获取高清封面、获取 LRC 歌词。   

### URL
获取直链: https://api.imvictor.tech/php-api/v7/cloudmusic/direct-link  
获取高清封面: https://api.imvictor.tech/php-api/v7/cloudmusic/cover  
获取 LRC 歌词:
https://api.imvictor.tech/php-api/v7/cloudmusic/lyrics

### HTTP 请求方式
GET
### 请求参数
#### `id` 必需
仅需传入歌曲 ID。  
**特别的，付费方可试听的歌曲不支持获取直链。**
### 返回类型
获取直链和获取高清封面，均返回一个 302 跳转，跳转到网易官方服务器。  
LRC 歌词则直接返回 UTF-8 编码的纯文本歌词。

### 请求示例
请求歌曲 [スパークル (movie ver.)](https://music.163.com/#/song?id=426881503) 的封面:  
- Request URL: https://api.imvictor.tech/php-api/v7/cloudmusic/cover?id=426881503  
- Request Method: GET  

### 返回数据示例
- Status Code: HTTP/2.0 302
- Location: https://p1.music.126.net/sSxbRt9RpC6s_MaewyDJfA==/18597139672292692.jpg

![https://api.imvictor.tech/php-api/v7/cloudmusic/cover?id=426881503](https://api.imvictor.tech/php-api/v7/cloudmusic/cover?id=426881503)

<b style="color: red;">请大家注意，不要无节制地使用。因为服务器的资源有限，谢谢。</b>


