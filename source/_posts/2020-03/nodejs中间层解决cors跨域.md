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

## 中间层项目新建

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

## app.js配置

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

## 跨域设置

对于浏览器来说，当请求的地址与当前网页地址的 协议(protocol)、主机(host/ip)、端口号(port)其中之一如果不相同，即为非同源。而非同源的请求，如果是非简单请求，就会出现跨域错误。

简单来说，对于非简单请求，浏览器会先发送一个options请求，附带当前请求的信息（如头部、参数等），如果非同源接口对此options请求验证通过，浏览器即可发起真正的get/post请求。中间层解决跨域的思路：是浏览器发起的所有请求先通过中间层，中间层再代理到实际请求的地址，当需要跨域时，中间层拦截浏览器发出的options请求，直接返回（不考虑第三方接口）浏览器`200`状态，告诉浏览器这个请求是被允许的，浏览器收到200响应后，便会发起真正的请求，此时由中间层直接代理转接到实际第三方接口地址，得到响应后转送给浏览器即可。

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