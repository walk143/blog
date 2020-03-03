---
title: nodejs中间层解决cors跨域
tags:
  - nodejs
  - 中间层
categories:
  - 技术
  - 前端
  - nodejs
date: 2020-03-02 22:40:49
---

通过nodejs中间层解决浏览器的跨域问题。大体思路为中间层拦截浏览器的`OPTIONS`请求，返回`200`成功状态及所需头部，使得浏览器可以继续发起实际请求
<!-- more -->

## 介绍

### 同源

目前，所有浏览器都实行同源政策。

同源包含三个方面。以`https://sloera.gitee.io/2020/03/02/2020-03/`为例

```yaml
协议相同: https:
域名相同: sloera.gitee.io
端口相同: 80 #网页默认端口是80，可省略
```

说明：

​	对于浏览器打开的A和B两个非同源网页。如果A的`Cookie`中包含了用户的登录信息，若没有同源政策，则B可获取A的`Cookie`，伪冒登录进行在A意愿之外的操作。所以需要同源政策来保护安全状态。

### CORS

由于同源政策，`AJAX`请求只能发给同源的网址。所以需要`CORS`（跨源资源分享Cross-Origin Resource Sharing）完成跨源`AJAX`请求。

`CORS`通信过程，由浏览器自动完成。浏览器发现`AJAX`请求跨源时，会自动添加一些附加头信息，甚至有时会多出一次`OPTIONS`请求，整个过程都由浏览器自动实现。

#### 请求分类

对于浏览器来说，`CORS`请求有两类：简单请求和非简单请求。

同时满足如下条件即为简单请求

```yaml
#1.请求方法为以下其一
method: 
	- HEAD
	- GET
	- POST
#2.请求头信息在以下字段之内
head:
	- Accept
	- Accept-Language
	- Content-Language
	- Last-Event-ID
	- Content-Type：只限于三个值application/x-www-form-urlencoded、multipart/form-data、text/plain #注意。`application/json`不在其中，所以如果post的请求体是json格式，必为非简单请求。
```

不属于简单请求的即为非简单请求

#### 简单请求

对于简单请求：浏览器在请求头信息中自动增加一个`Origin`字段，用来说明本次请求来自哪个源（协议、域名、端口），服务器根据这些信息判定是否同意请求。

如果`Origin`指定的域名在许可范围内（中间层一般不加判断，都准予许可，直接设置res的头信息返回），服务器返回的响应会多出几个与`CORS`相关的头信息字段。

```yaml
Access-Control-Allow-Origin: http://localhost:8080 #必须，请求时的`Origin`或者`*`
Access-Control-Allow-Credentials: true #可选，布尔值。是否允许发送`Cookie`
Access-Control-Expose-Headers: test #可选。用于XMLHttpRequest对象的getResponseHeader()取值。
```

#### 非简单请求



## 解决

中间层解决跨域的思路：是浏览器发起的所有请求先通过中间层，中间层再代理到实际请求的地址，当需要跨域时，中间层拦截浏览器发出的options请求，直接返回（不考虑第三方接口）浏览器`200`状态，告诉浏览器这个请求是被允许的，浏览器收到200响应后，便会发起真正的请求，此时由中间层直接代理转接到实际第三方接口地址，得到响应后转送给浏览器即可。

### 中间层项目新建

```sh
mkdir middlelayer #创建项目目录
cd middlelayer #进入项目目录
cnpm init #项目初始化
####接下来会进入交互，可以自定义项目。只有当出现`	entry point: (index.js)`的时候，输入`app.js`再回车
package name: (middlelayer)#项目名称
version: (1.0.0) #版本
description: 中间层跨域解决测试项目 #描述
entry point: (index.js) app.js #项目入口
test command: #测试命令 未知，暂时空白
git repository: #git仓库，配置后不知道如何生效，暂时空白
keywords: cors#关键词
author: sloera #作者
license: ISC # 应该是版权
#####项目新建结束
#默认只有一个package.json文件
#用vsc打开目录作为项目工作空间
#新建app.js文件
```

安装 Express 并将其保存到依赖列表中

```sh
$ cnpm install express --save
```

### app.js配置

```javascript
const express = require('express') //引入express
const app = express() //使用express
app.listen(3000) //监听3000端口

//配置完毕，
node app.js //启动此工程
```

### 使用config配置文件

根目录新建`config`目录，再创建`config.json`文件

配置文件内容为json格式

```json
{
    "port": 3000
}
```

```javascript
const config = require('./config/config.json') //使用相对路径引用
//当作常规json对象取值。如`config.port`
```

### 配置git

```sh
touch .gitignore #创建gitignore文件，通过此文件，git确定哪些文件不会进行跟踪
#####内容开始
node_modules/
#####内容结束
#暂时只有模版文件不需要
```

### 跨域解决

在`allowCores`中，设置了响应头，并判断如果请求类型是`OPTIONS`类型，直接返回`200`成功状态，不走后面的代理。所以下面这段，应该放到`app.js`其他代理逻辑之前。

```javascript
var allowCores = function(req, res, next) {
    res.header('Access-Control-Allow-Origin', '*'); //设置允许的来源
    res.header('Access-Control-Allow-Methods', 'GET,PUT,POST,DELETE,OPTIONS');// 允许的请求方法
    res.header('Access-Control-Allow-Headers', 'Content-Type,target,ccc,DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X_Requested_With,If-Modified-Since,Cache-Control, Accept-Language, Origin, Accept-Encoding'); //允许的头部
    res.header('Access-Control-Allow-Credentials','true'); //
    if (req.method == 'OPTIONS') { //如果是options方法，直接返回200.解决跨域响应问题
        res.sendStatus(200)
    } else {
        next()
    } 
}
app.use(allowCores); //使用跨域中间件
```



## 其他

### app.use与app.all

`app.all(path,[callback1[,callback2]])`:可接受多个回调函数作为`路由处理器`，往往要处理`请求`和`响应`。`path`匹配`完整`路径，接受正则，匹配所有方法，执行顺序按照顺序。
`app.all(path,callback1,callback2)`等同于
`app.all(path,callback1)` 和 `app.all(path,callback2)`，2在后面。

概况起来：
`app.use`用作中间件，调用顺序是书写顺序。
`app.all`用作路由处理，匹配完整路径，在`app.use`之后。
`use`的path为路径开头，回调函数的路径为路径的声誉部分，use的路径是完整的了，回调只需要写`/`，即回调执行的`完整路径`是 `usePath+callbackPath`。
`all`的回调函数的路径必须和`app`的路径一致。

## 参考

> <http://www.ruanyifeng.com/blog/2016/04/cors.html>