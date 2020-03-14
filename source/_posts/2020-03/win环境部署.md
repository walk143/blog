---
title: win环境部署
date: 2020-03-13 22:56:46
tags:
categories:
---
 
介绍win系统重装后的环境搭建。
<!-- more -->

# 互换ctrl caps

> https://blog.csdn.net/qq_15210067/article/details/78260391

修改注册表
`计算机\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Keyboard Layout`
在菜单右键新建二进制项`Scancode Map`。
```yaml
00 00 00 00 00 00 00 00 
03 00 00 00 1D 00 3A 00 
3A 00 1D 00 00 00 00 00
```

# node

重装系统后，环境变量会丢失。需要将`npm`路径添加到系统环境变量中。

定位到`nodejs\npm`的路径，如`D:\Program Files\nodejs`,添加系统变量<sup>path</sup>。可先添加一个`变量名：NODE_PATH;变量值：D:\Program Files\nodejs`的变量，然后在`Path`中添加`%NODE_PATH%`。也可直接在`Path`中添加路径。

`npm`安装的全局模块默认在`%appdata%\npm`路径。所以还需要添加此路径到系统环境变量`Path`中。使得安装后的模块命令可直接调用。

配置变量后安装`cnpm`代替`npm`

```cmd
npm install cnpm -g --registry=https://registry.npm.taobao.org
npm config set registry https://registry.npm.taobao.org
npm config get registry
```

# 右键菜单添加

修改注册表

大体分为三类：
* `HKEY_CLASSES_ROOT\*\shell`：对文件的菜单
* `HKEY_CLASSES_ROOT\Directory\shell`：对目录的菜单
* `HKEY_CLASSES_ROOT\Directory\Background\shell`：对空白处的菜单

添加`VSCode`的示例如下
```reg
Windows Registry Editor Version 5.00

[HKEY_CLASSES_ROOT\*\shell\VSCode]
;此行为注释，@为新建项的默认值 Icon为新建的一个值。在`.reg`文件中写路径需要加转义。直接修改注册表不需要转义
;此项为打开文件的右键菜单
@="Edit with Code"
"Icon"="D:\\Softs\\VSCode\\code.exe"

[HKEY_CLASSES_ROOT\*\shell\VSCode\command]
;command项说明右键执行的具体命令
@="\"D:\\Softs\\VSCode\\Code.exe\" \"%1\""

[HKEY_CLASSES_ROOT\Directory\shell\VSCode]
@="Open with Code"
"Icon"="D:\\Softs\\VSCode\\Code.exe"

[HKEY_CLASSES_ROOT\Directory\shell\VSCode\command]
;注意目录的参数为 "%V"
@="\"D:\\Softs\\VSCode\\Code.exe\" \"%V\""

[HKEY_CLASSES_ROOT\Directory\Background\shell\VSCode]
@="Open with Code"
"Icon"="D:\\Softs\\VSCode\\Code.exe"

[HKEY_CLASSES_ROOT\Directory\Background\shell\VSCode\command]
@="\"D:\\Softs\\VSCode\\Code.exe\" \"%V\""
```

# powershell

禁止运行脚本。

管理员权限运行`Power Shell`。输入`set-executionpolicy remotesigned`,交互输入`y`

# git

添加`git`系统环境变量<sub>path</sub>。

`GIT_PATH:D:\Softs\PortableGit`,添加系统变量`Path:path;%GIT_PATH%\bin`

# chrome

~~插件离线安装。

新安装的chrome。在地址栏输入`chrome://extensions`，打开`开发者模式`，将下载好的crx拖入后，可能会提示`只能通过Chrome网上应用店添加此项内容`。此时可以将crx文件解压，用chrome的`加载已解压的扩展程序`来添加，可以看到扩展程序的`id`。如果不可启动，通过组策略将id添加到白名单，通过crx重新添加。~~

在[极简插件](https://chrome.zzzmh.cn/index)下载[谷歌上网助手](https://chrome.zzzmh.cn/info?token=cieikaeocafmceoapfogpffaalkncpkc)，用临时邮箱注册一个账号，通过助手访问谷歌商店然后下载插件。

## Quick QR Code

https://chrome.google.com/webstore/detail/quick-qr-code-generator/afpbjjgbdimpioenaedcjgkaigggcdpp?utm_source=chrome-ntp-icon
将当前网址生成二维码

## Keepass Http Connector

https://chrome.google.com/webstore/detail/keepasshttp-connector/dafgdjggglmmknipkhngniifhplpcldb?utm_source=chrome-ntp-icon
连接keepass密码管理器

点击进行设置。进行`Connected Databases`设置。打开keepass客户端软件，点击插件设置的`connect`会弹出连接页面.

## floccus

https://chrome.google.com/webstore/detail/floccus-bookmarks-sync/fnaicdffflnofjppbagibeoednhnbjhg?utm_source=chrome-ntp-icon

设置见{% post_link 2020-02/浏览器书签同步floccus %}。

## vimium

https://chrome.google.com/webstore/detail/vimium/dbepggeogbaibhgnhhndojpepiihcmeb?utm_source=chrome-ntp-icon
vim方式通过键盘控制浏览器行为

## infinity

https://chrome.google.com/webstore/detail/infinity-new-tab-pro/nnnkddnnlpamobajfibfdgfnbcnkgngh?utm_source=chrome-ntp-icon
自定义新的标签页

## OneTab

https://chrome.google.com/webstore/detail/onetab/chphlpgkkbolifaimnlloiipkdnihall?utm_source=chrome-ntp-icon
可以将标签页转换成一个列表暂存，节省内存。

## 划词翻译

https://chrome.google.com/webstore/detail/%E5%88%92%E8%AF%8D%E7%BF%BB%E8%AF%91/ikhdkkncnoglghljlkmcimlnlhkeamad?utm_source=chrome-ntp-icon
翻译

## Adblock Plus

https://chrome.google.com/webstore/detail/adblock-plus-free-ad-bloc/cfhdojbkjhnklbpkdaibdccddilifddb?utm_source=chrome-ntp-icon
广告拦截器

## Tampermonkey

https://chrome.google.com/webstore/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo?utm_source=chrome-ntp-icon
浏览器插件脚本

## IE Tab

https://chrome.google.com/webstore/detail/ie-tab/hehijbfgiekmjfkfjpbkbammjbdenadd?utm_source=chrome-ntp-icon
兼容ie内核

## Vue.js devtools

https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd?utm_source=chrome-ntp-icon
Vue调试插件

## Extension Manager

https://chrome.google.com/webstore/detail/extension-manager/gjldcdngmdknpinoemndlidpcabkggco?utm_source=chrome-ntp-icon
扩展管理器

## Octotree

https://chrome.google.com/webstore/detail/octotree/bkhaagjahfmjljalopjnoealnfndnagc?utm_source=chrome-ntp-icon
github树形展示

## JSON-handle

https://chrome.google.com/webstore/detail/json-handle/iahnhfdhidomcpggpaimmmahffihkfnj?utm_source=chrome-ntp-icon
处理json数据


# 说明

path: 可用`win+Pause`键打开`控制面板\系统和安全\系统`，选择`高级系统设置`，修改`系统属性\高级\环境变量`，添加系统变量。