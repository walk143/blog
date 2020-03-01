---
title: win系统安装-u盘方式
date: 2020-02-29 23:51:27
tags:
    - win
    - 系统
    - 安装
categories:
    - 安装
    - win
---

## 准备工具

整个安装环境需要一台联网电脑及一个空白u盘（容量>8G)。

1. 下载镜像

   win镜像可通过{% asset_link https://www.microsoft.com/zh-cn/software-download/windows10 微软官方 %}或者{% asset_link https://msdn.itellyou.cn/ "i tell you" %}网站下载。

   1. 微软官方。点击`立即下载工具`，下载`MediaCreationTool.exe`工具。使用win系统管理员登录（如果不知道是不是，那就肯定是）运行此程序。此工具可下载镜像、制作启动u盘或者给升级本电脑系统。
   2. i tell you。点击`操作系统`，展开系统列表，选择需要的系统镜像进行下载（建议win10）。`ed2k`通过迅雷下载即可。

2. 烧录工具

   下载{% asset_link https://rufus.en.softonic.com/ rufus%}（win镜像烧录工具，linux为etcher）。