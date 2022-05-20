---
title: 关于 jsdelivr CDN 被污染的解决方案
date: 2022-05-20 20:45:40
updated: 2022-05-20 20:45:40
categories:
  - 其他
comments: true
---
### 前言

好像最近 jsdelivr 的 CDN 又被污染了，希望 jsdelivr 可以早日修复，不被滥用qwq

### 1. 切换一些其他 CDN

- 字节静态库：`cdn.bytedance.com`
- 360静态库：`cdn.baomitu.com`
- 七牛静态库：`staticfile.org`
- bootcdn：`bootcdn.cn`
- unpkg：`unpkg.com`

这种方法一般比较稳定，速度比较快，但切换起来比较麻烦，而且有的静态库的内容不够完整，更新不够快。

### 2. 使用 jsdelivr 官方子域

- 默认：`cdn.jsdelivr.net`
- CloudFlare：`test1.jsdelivr.net`
- CloudFlare：`testingcf.jsdelivr.net`
- Fastly：`fastly.jsdelivr.net`
- GCORE：`gcore.jsdelivr.net`

推荐使用第 3 个，也可以根据速度使用适合的。

只需把网址前的 `cdn` 修改成对应的即可。

这种方法一时比较方便，但可能不长久也会被污染，且用且珍惜吧。