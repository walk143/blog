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


# 说明

path: 可用`win+Pause`键打开`控制面板\系统和安全\系统`，选择`高级系统设置`，修改`系统属性\高级\环境变量`，添加系统变量。