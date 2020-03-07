---
title: wsl安装nodejs
tags:
  - wsl
  - nodejs
  - 安装
categories:
  - 安装
  - wsl
date: 2020-03-02 23:15:46
---

介绍nodejs在wsl（win子系统）上的安装。同时安装`cnpm`替代`npm`命令.
<!-- more -->

# npm安装配置

## 安装npm

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

## 配置cnpm

```sh
npm install cnpm -g --registry=https://registry.npm.taobao.org
npm config set registry https://registry.npm.taobao.org
npm config get registry
```

以后即可使用`cnpm`代替`npm`命令

使用`cnpm -v`测试是否安装成功

# 命令简介

## 安装特定版本

### 查看历史版本

```sh
#以http-proxy-middleware为例
#查看最新版本
root@DESKTOP-K09C8AF:/mnt/j/data/gitee/sloera# cnpm view http-proxy-middleware version
1.0.1
#查看所有版本
root@DESKTOP-K09C8AF:/mnt/j/data/gitee/sloera# cnpm view http-proxy-middleware versions
 
[
  '0.0.1',         '0.0.2',         '0.0.3',      
  '0.0.4',         '0.0.5',         '0.1.0',      
  '0.2.0',         '0.3.0',         '0.3.1',      
  '0.3.2',         '0.4.0',         '0.5.0',      
  '0.6.0',         '0.7.0',         '0.8.0',      
  '0.8.1',         '0.8.2',         '0.9.0',      
  '0.9.1',         '0.10.0-beta',   '0.10.0',     
  '0.11.0',        '0.12.0',        '0.13.0',     
  '0.14.0',        '0.15.0',        '0.15.1-beta',
  '0.15.1',        '0.15.2',        '0.16.0',     
  '0.17.0-beta',   '0.17.0',        '0.17.1',     
  '0.17.2-beta',   '0.17.2',        '0.17.3',     
  '0.17.4',        '0.18.0',        '0.19.0',
  '0.19.1',        '0.20.0-beta.0', '0.20.0-beta.1',
  '0.20.0-beta.2', '0.20.0',        '0.21.0-beta.1',
  '0.21.0-beta.2', '0.21.0-beta.3', '0.21.0',
  '0.22.0-alpha',  '1.0.0',         '1.0.1'
]
#查看具体信息
root@DESKTOP-K09C8AF:/mnt/j/data/gitee/sloera# cnpm info http-proxy-middleware

http-proxy-middleware@1.0.1 | MIT | deps: 5 | versions: 51
The one-liner node.js proxy middleware for connect, express and browser-sync
https://github.com/chimurai/http-proxy-middleware#readme

dist
.tarball: https://registry.npm.taobao.org/http-proxy-middleware/download/http-proxy-middleware-1.0.1.tgz
.shasum: a87ee6564991faca4844ae4ab1cf4221279c28f0

dependencies:
@types/http-proxy: ^1.17.3 http-proxy: ^1.18.0        is-glob: ^4.0.1            lodash: ^4.17.15           micromatch: ^4.0.2

maintainers:
- chimurai <stevenchim@gmail.com>

dist-tags:
alpha: 0.22.0-alpha  beta: 0.21.0-beta.3  latest: 1.0.1

published a week ago by chimurai <stevenchim@gmail.com>
```

### 安装指定版本

包名后带`@版本号`

```sh
#全局安装http-proxy-middleware的0.21.0版本
cnpm install  --save http-proxy-middleware@0.21.0
```