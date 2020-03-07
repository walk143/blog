---
title: linux安装docker
date: 2020-03-06 21:58:19
tags:
    - linux
    - 安装
    - docker
    - centos8
categories:
    - 安装
    - linux
---

介绍在linux上安装docker。配置ubuntu/centos使用清华镜像源。配置docker走国内代理。
<!-- more -->

# Docker简介

Docker概念说明。

- 镜像(Image)：一个不可写的模版。容器是基于此运行的。
- 容器(Container)：将一个镜像运行起来。一个容器相当于依据镜像new的对象。容器（对象）依据镜像模版（类）来创建。
- 仓库(Repository)：一个保存镜像的地方。
- Docker客户端(Client)：客户端通过命令行或者其他工具使用`Docker SDK`与Docker的守护进程通信。
- Docker主机(Host)：运行docker的地方。 
- Docker Machine：是一个简化Docker安装的命令行工具

Docker使用客户端-服务器(C/S)架构模式。

# Docker安装

## ~~ubuntu安装~~

由于wsl安装docker后不可运行。停止此方法。

### ubuntu更换清华源

由于正常访问太慢，需更换镜像为清华源

编辑`/etc/apt/sources.list`文件，将原有内容都注释

添加如下

```yaml
deb https://mirrors.ustc.edu.cn/ubuntu/ bionic main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
```

然后执行`apt update`更新

### 安装docker

```sh
#卸载旧版本
apt-get remove docker docker-engine docker.io containerd runc
#更新apt包索引
apt update
#安装apt依赖包，用于通过https获取仓库
apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
#添加 Docker 的官方 GPG 密钥：
#curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
#由于已经更换为清华镜像源，所以使用如下命令 使用清华镜像代替docker官方
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
#验证是否拥有带指纹的密钥
apt-key fingerprint 0EBFCD88
#设定稳定版仓库 使用清华镜像代替docker官方
add-apt-repository \
   "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu \
  $(lsb_release -cs) \
  stable"
#重新更新索引
apt-get update
#安装最新版本的 Docker Engine-Community 和 containerd 
apt-get install docker-ce docker-ce-cli containerd.io
#安装特定版本
	#列出可用版本
	apt-cache madison docker-ce
	#选择安装 使用第二列中的版本字符串安装特定版本
	apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io
#测试是否安装成功
docker run hello-world
```

## centos安装

### centos更换清华源

由于正常访问太慢，需更换镜像为清华源

教程：<https://mirrors.tuna.tsinghua.edu.cn/help/centos/>

```sh
#备份 CentOS-Base.repo
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak
[root@localhost yum.repos.d]# cp CentOS-AppStream.repo CentOS-AppStream.repo.bak
[root@localhost yum.repos.d]# cp CentOS-PowerTools.repo CentOS-PowerTools.repo.bak
[root@localhost yum.repos.d]# cp CentOS-Extras.repo CentOS-Extras.repo.bak
[root@localhost yum.repos.d]# cp CentOS-centosplus.repo CentOS-centosplus.repo.bak
#确认centos版本
[root@localhost walker]# rpm -q centos-release
centos-release-8.0-0.1905.0.9.el8.x86_64
#新建源
vi /etc/yum.repos.d/CentOS-Base.repo
#分别复制内容
[root@localhost walker]# vi /etc/yum.repos.d/CentOS-Base.repo
# CentOS-Base.repo
#
# The mirror system uses the connecting IP address of the client and the
# update status of each mirror to pick mirrors that are updated to and
# geographically close to the client.  You should use this for CentOS updates
# unless you are manually picking other mirrors.
#
# If the mirrorlist= does not work for you, as a fall back you can try the
# remarked out baseurl= line instead.
#
#
#1.到Base文件 替换原来的内容
[BaseOS]
name=CentOS-$releasever - Base
baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos/$releasever/BaseOS/$basearch/os/
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=BaseOS&infra=$infra
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial

#2.AppStream 替换原来的内容
name=CentOS-$releasever - AppStream
baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos/$releasever/AppStream/$basearch/os/
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=AppStream&infra=$infra
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial

#3.PowerTools 替换原来的内容
name=CentOS-$releasever - PowerTools
baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos/$releasever/PowerTools/$basearch/os/
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=PowerTools&infra=$infra
enabled=0
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial

#3.Extras 替换原来的内容
name=CentOS-$releasever - Extras
baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos/$releasever/extras/$basearch/
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-7

#3.Plus 替换原来的内容
name=CentOS-$releasever - Plus
baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos/$releasever/centosplus/$basearch/
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-7
#更新软件包缓存
yum clean all
yum makecache
```

可能出现“错误 ID repo: Base OS, byte =   4”错误。

去除[]内的空格即可。

### 安装docker

```sh
#卸载旧版本
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
#安装所需的软件包。yum-utils 提供了 yum-config-manager ，并且 device mapper 存储驱动程序需要 device-mapper-persistent-data 和 lvm2
yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
#设置稳定仓库
yum-config-manager \
    --add-repo \
    https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/centos/docker-ce.repo
#软件仓库地址替换为TUNA
sed -i 's+download.docker.com+mirrors.tuna.tsinghua.edu.cn/docker-ce+' /etc/yum.repos.d/docker-ce.repo
yum makecache
#安装最新版本的 Docker Engine-Community 和 containerd 
yum install docker-ce docker-ce-cli containerd.io
	#如报错 package containerd.io-1.2.10-3.2.el7.x86_64 is excluded
    #在https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/centos/7/x86_64/stable/Packages/ 找到一个版本，复制链接
    yum -y install https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/centos/7/x86_64/stable/Packages/containerd.io-1.2.12-3.1.el7.x86_64.rpm
    #重新运行 yum install docker-ce docker-ce-cli。已去除containerd.io
#Docker 安装完默认未启动。并且已经创建好 docker 用户组，但该用户组下没有用户。
#安装特定版本
	#列出可用版本
	yum list docker-ce --showduplicates | sort -r
	#通过其完整的软件包名称安装特定版本，该软件包名称是软件包名称（docker-ce）加上版本字符串（第二列），从第一个冒号（:）一直到第一个连字符，并用连字符（-）分隔。例如：docker-ce-18.09.1。
	yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
#docker安装完默认未启动。并且已经创建好 docker 用户组，但该用户组下没有用户。
#启动docker
systemctl start docker
#测试是否安装成功
docker run hello-world
```

## 配置国内源

国内加速站点

* ~~https://registry.docker-cn.com~~不建议
* http://hub-mirror.c.163.com
* https://3laho3y3.mirror.aliyuncs.com
* http://f1361db2.m.daocloud.io
* https://mirror.ccs.tencentyun.com

```
#https://registry.docker-cn.com
http://hub-mirror.c.163.com
https://3laho3y3.mirror.aliyuncs.com
http://f1361db2.m.daocloud.io
https://mirror.ccs.tencentyun.com
```



```sh
vi /etc/docker/daemon.json
#####输入以下内容
{
  "registry-mirrors": ["<your accelerate address>"]
}
###### 结束
#实际值
[root@localhost ~]# cat /etc/docker/daemon.json 
{
  "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
#重启
#systemctl daemon-reload
systemctl restart docker.service
```

