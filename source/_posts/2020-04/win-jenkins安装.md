---
title: win-jenkins安装
date: 2020-04-01 16:59:28
tags:
categories:
---

介绍win下jenkins下的安装
<!-- more -->

# 下载

[官网](https://jenkins.io/zh/)下载win压缩包，执行msi程序进行安装

# 修改配置

1. 修改`${jenkins_home}/Jenkins/updates/default.json`，将内置的`http://updates.jenkins-ci.org/download/plugins`都换成`https://mirrors.tuna.tsinghua.edu.cn/jenkins/plugins/`镜像地址。
2. 删除整个`signature`节点。
3. `${jenkins_home}/jenkins.xml`可修改端口为8001。

# 启动

1. msi安装后，jenkins已经启动后台服务，修改端口号后，使用`net stop jenkins`，`net start jenkins`重启
2. 安装推荐的插件。
3. 