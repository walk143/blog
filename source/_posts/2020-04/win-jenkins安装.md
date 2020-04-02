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

# 修改启动配置

1. 修改`${jenkins_home}/Jenkins/updates/default.json`，将内置的`http://updates.jenkins-ci.org/download/plugins`都换成`https://mirrors.tuna.tsinghua.edu.cn/jenkins/plugins/`镜像地址。
2. 删除整个`signature`节点。
3. `${jenkins_home}/jenkins.xml`可修改端口为8001。

# 启动

1. msi安装后，jenkins已经启动后台服务，修改端口号后，使用`net stop jenkins`，`net start jenkins`重启
2. 安装推荐的插件。

#  全局配置

## Global Tool Configuration

1. Maven配置
   默认（全局）settings提供：`D:\Softs\apache-maven-3.3.9\conf\settings.xml`。配置自己本地mavensettings文件
2. JDK
   配置本地安装jdk目录：`C:\Program Files\Java\jdk1.8.0_211`。
3. Git
   链接到本地的git可执行程序。`C:\Program Files\Git\bin\git.exe`。
4. Maven
   新增Maven，`MAVEN_HOME`为：`D:\Softs\apache-maven-3.3.9`
5. NodeJS
   别名：nodeJS
   安装目录：`D:\Softs\nodejs`
6. 进行保存。

## 

1. 全局属性
   Environment variables
   新建键值对。LANG/zh_CN.UTF-8
2. 修改`jenkins.xml`，arguments最后添加`-Dfile.encoding=utf-8`

# 插件安装
1. Maven Integration
   集成maven
2. Deploy to container
   构建后部署到tomcat
3. NodeJS
4. SSH Agent
5. 
6. 

# tomcat配置

1. 复制一个新的tomcat
2. 配置`conf/server.xml`
   1. shutdown端口：9203
   2. connector port http：9201
   3. redirectPort: 9204
   4. 注释：<Connector port="8017" protocol="AJP/1.3" redirectPort="8443" />
3. 配置`conf/tomcat-users.xml`
   增加部署用的用户
   ```xml
    <role rolename="manager-script" />
    <role rolename="manager-gui" />
    <user username="deployer" password="deployer" roles="manager-script,manager-gui" />
   ```
4. 配置`bin/catalina.bat`
   开头添加：`CATALINA_HOME=D:\Softs\apache-tomcat-9.0.22-jenkins`
5. 配置`bin/startup.bat`
   开头添加：
   ```xml
    set TITLE="8100"
    set CATALINA_BASE=D:\Softs\apache-tomcat-9.0.22-jenkins
    set CATALINA_HOME=D:\Softs\apache-tomcat-9.0.22-jenkins
   ```
6. jenkins添加tomcat凭证。`deployer/deployer`

# git配置
新建一个git密钥

将公钥添加到gitlab服务器上

添加的时候，去除结尾多余的回车
