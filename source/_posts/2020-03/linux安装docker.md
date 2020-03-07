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

简单介绍对镜像容器的基本操作。

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

# 简单使用

## 容器使用

### 查看

#### 查看docker运行容器

```sh
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
016c314dde0e        centos              "/bin/bash"         2 minutes ago       Up 2 minutes                            centos
```

#### 查看所有容器

```sh
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                         PORTS               NAMES
016c314dde0e        centos              "/bin/bash"         3 minutes ago       Up 2 minutes                                       centos
6d279c5482d8        centos              "/bin/bash"         About an hour ago   Exited (0) About an hour ago                       adoring_heyrovsky
3f15dc78658b        hello-world         "/hello"            4 hours ago         Exited (0) 4 hours ago                             dreamy_darwin
```

### 交互运行


#### 在公共仓库搜索centos的镜像

```sh
docker search centos
```

#### 交互模式运行centos。

```sh
#-it 代表交互模式。如果本地没有centos镜像，首次运行会从公共仓库自动下载
[root@localhost ~]# docker run -it centos /bin/bash
Unable to find image 'centos:latest' locally
latest: Pulling from library/centos
8a29a15cefae: Pull complete 
Digest: sha256:fe8d8242200
Status: Downloaded newer image for centos:latest
[root@6d279c5482d8 /]# 
#如上，已经进入了centos命令行界面,此时，输出exit会导致容器停止
```

参数解析

```yaml
-t: 在新容器内指定一个伪终端或终端。
-i: 允许你对容器内的标准输入 (STDIN) 进行交互。
```

### 后台运行

#### 指定后台运行并命名为centos

```sh
docker run -itd --name centos centos /bin/bash 
```

#### 重新进入容器交互

```sh
docker exec -it centos /bin/bash #推荐！！注：centos为之前--name 后面的命名 此种方式进入后，输入exit不会导致容器停止。
docker attach centos #此种方式进入后，输入exit会导致容器停止。
```

### 启停相关

```sh
docker start/stop/restart <容器ID>
```

### 删除容器

```sh
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                         PORTS               NAMES
016c314dde0e        centos              "/bin/bash"         7 minutes ago       Up 7 minutes                                       centos
6d279c5482d8        centos              "/bin/bash"         About an hour ago   Exited (0) About an hour ago                       adoring_heyrovsky
3f15dc78658b        hello-world         "/hello"            4 hours ago         Exited (0) 4 hours ago                             dreamy_darwin
[root@localhost ~]# docker rm 6d2
6d2
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                   PORTS               NAMES
016c314dde0e        centos              "/bin/bash"         7 minutes ago       Up 7 minutes                                 centos
3f15dc78658b        hello-world         "/hello"            4 hours ago         Exited (0) 4 hours ago                       dreamy_darwin
```

### 导出和导入容器

#### 导出

```sh
docker export <容器ID> > <容器name>
[root@localhost temp]# ls
[root@localhost temp]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                   PORTS               NAMES
016c314dde0e        centos              "/bin/bash"         4 hours ago         Up 4 hours                                   centos
3f15dc78658b        hello-world         "/hello"            7 hours ago         Exited (0) 7 hours ago                       dreamy_darwin
[root@localhost temp]# docker export 3f1 > hello.tar
[root@localhost temp]# ls
hello.tar
```

#### 导入

```sh
cat /path/<容器name> | docker import - test/hello:v1
[root@localhost temp]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              latest              470671670cac        7 weeks ago         237MB
hello-world         latest              fce289e99eb9        14 months ago       1.84kB
[root@localhost temp]# ls
hello.tar
[root@localhost temp]# cat hello.tar | docker import - test/hello:v1
sha256:3c1a49673aae7b946b37c06ac88e4576af551f3924b67cdf473a12b62f435a09
[root@localhost temp]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
test/hello          v1                  3c1a49673aae        5 seconds ago       1.85kB
centos              latest              470671670cac        7 weeks ago         237MB
hello-world         latest              fce289e99eb9        14 months ago       1.84kB
```

### 与宿主机复制文件

使用`docker cp`命令，docker容器内路径前加`<容器name/id>:`标识。

```sh
[root@localhost temp]# ls
hello.tar
[root@localhost temp]# docker cp 0c:/home/aaa ./
[root@localhost temp]# ls
aaa  hello.tar
```

## 镜像使用

### 查看

```sh
docker images
[root@localhost temp]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
test/hello          v1                  3c1a49673aae        5 seconds ago       1.85kB
centos              latest              470671670cac        7 weeks ago         237MB
hello-world         latest              fce289e99eb9        14 months ago       1.84kB
```

选项说明：

- **REPOSITORY：**表示镜像的仓库源
- **TAG：**镜像的标签
- **IMAGE ID：**镜像ID
- **CREATED：**镜像创建时间
- **SIZE：**镜像大小

### 获取新镜像

如果直接使用`docker run <镜像>`，当本地不存在该镜像时，会自动从公共仓库下载。

也可以使用`docker pull <镜像>`下载。

### 删除镜像

```sh
docker rmi <镜像>
[root@localhost temp]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
test/hello          v1                  3c1a49673aae        5 minutes ago       1.85kB
centos              latest              470671670cac        7 weeks ago         237MB
hello-world         latest              fce289e99eb9        14 months ago       1.84kB
[root@localhost temp]# docker rmi test/hello:v1 
Untagged: test/hello:v1
Deleted: sha256:3c1a49673aae7b946b37c06ac88e4576af551f3924b67cdf473a12b62f435a09
Deleted: sha256:603326ea154eb574206f85ee9e947a9e1791363cb67b60d9969ce363335022c3
```

### 更新镜像

更新镜像首先需要启动一个容器，运行容器后，对内容进行修改。然后可以通过commit来保存容器的修改即为更新镜像。

```sh
[root@localhost temp]# docker exec -it centos /bin/bash
[root@016c314dde0e /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@016c314dde0e /]# cd home/
[root@016c314dde0e home]# ls
[root@016c314dde0e home]# touch aaa
[root@016c314dde0e home]# exit
exit
[root@localhost temp]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
016c314dde0e        centos              "/bin/bash"         4 hours ago         Up 4 hours                              centos
[root@localhost temp]# docker commit -m="更新测试" -a="sloera" 016 sloera/centos:v1
sha256:c48b282a5ea728abcadccb193eaac703d00bc394ef87197a427c3bf2cd5c9ec4
[root@localhost temp]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
sloera/centos       v1                  c48b282a5ea7        13 seconds ago      237MB
centos              latest              470671670cac        7 weeks ago         237MB
hello-world         latest              fce289e99eb9        14 months ago       1.84kB
```

提交参数说明：

- **-m:** 提交的描述信息
- **-a:** 指定镜像作者
- **016：**容器 ID，唯一标识即可
- **sloera/centos:v1:** 指定要创建的目标镜像名

启用新镜像测试

```sh
[root@localhost temp]# docker stop centos 
centos
[root@localhost temp]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[root@localhost temp]# docker run -tid sloera/centos:v1 /bin/bash #可用--name指定容器名称
0c2f836fc15c6e4d3af65bc57f9695e1304d0b35dcec0a6323666a3328592d27
[root@localhost temp]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
0c2f836fc15c        sloera/centos:v1    "/bin/bash"         16 seconds ago      Up 15 seconds                           nice_feynman
[root@localhost temp]# docker exec -it 0c /bin/bash
[root@0c2f836fc15c /]# ls home/
aaa

```

