---
title: wsl安装nodejs
tags:
  - wsl
  - nodejs
  - 安装
categories:
  - 安装
  - linux
date: 2020-03-02 23:15:46
---


介绍nodejs在wsl（win子系统）上的安装。同时安装`cnpm`替代`npm`命令
<!-- more -->

## npm安装配置

可在[nodejs中文官网](<http://nodejs.cn/>)下载。

```sh
pwd
#/softs/package/nodejs
wget https://npm.taobao.org/mirrors/node/v12.16.1/node-v12.16.1-linux-x64.tar.xz # 下载
cd ../../
mkdir nodejs
tar -Jxf package/nodejs/node-v12.16.1-linux-x64.tar.xz -C nodejs/ #解压tar包到nodejs目录下
cd nodejs
ln -s node-v12.16.1-linux-x64/ node #创建指向nodde-v12.16~的链接node
#结果如下
#lrwxrwxrwx 1 root root   24 Mar  2 23:30 node -> node-v12.16.1-linux-x64//
#drwxr-xr-x 1 1001 1001 4096 Feb 18 12:35 node-v12.16.1-linux-x64/
cd node/bin/
ln -s /softs/nodejs/node/bin/node /usr/local/bin/node #创建node到系统环境上下文，否则直接输入node导致系统找不到命令
ln -s /softs/nodejs/node/bin/npm /usr/local/bin/npm #创建npm的链接
node -v #测试是否安装成功
```

命令说明

```yaml
tar: 命令，用于打包（将几个文件合并成一个，结尾为`.tar`，配合参数可实现打包压缩
    -J: xz压缩算法。
    -x: 解压tar包
    -c: 创建tar包
    -f: 指定包名，即后面的`package/nodejs/node-v12.16.1-linux-x64.tar.xz` #-f后必须立即跟包名
    -C: 指定解压路径，后面可跟相对于当前目录的相对路径，也可指定中对于`/`的绝对路径
    #对于多个参数，可只用一个-引导，所以上述命令为：
    #解压以xz压缩算法打包、包名为`~node-~.tar.xz`的包 到 `nodejs/`目录下。

ln: 创建链接命令。相当于win的快捷方式。有硬链接及软链接之分。
	-s: 创建软链接。后面跟要创建链接的对象 再跟创建链接的目录或者快捷方式名
```

## 安装cnpm

```sh
npm install cnpm -g --registry=https://registry.npm.taobao.org
npm config set registry https://registry.npm.taobao.org
npm config get registry
```

以后即可使用`cnpm`代替`npm`命令

使用`cnpm -v`测试是否安装成功