---
title: git
date: 2020-02-25 23:03:38
tags: 
    - git
    - 工具
    - 配置
categories:
   - 工具
   - 软件
   - git
---

介绍git的安装配置过程。主要包括配置密钥，连接服务器git仓库。还包含了部分git操作介绍。
<!-- more -->

## git安装

1. 下载git

   <https://git-scm.com/download/win>

   可下载portable版本。 {% asset_link PortableGit-2.23.0-64-bit.7z.exe PortableGit-2.23.0-64-bit.7z.exe %}

2. 解压到自定义目录

   添加`自定义目录/bin`到`系统变量>Path`
<!-- more -->
   {% asset_img 1582557257454.png 环境变量 %}

   自定义目录下，`git-bash`即为常用bash窗口。

   {% asset_img 1582557334851.png PortableGit文件 %}

   > 以下如有命令，即为在`git-bash`中执行

3. 生成密钥

   ```sh
   ssh-keygen -t rsa -C "name@email.com" # ""内为自己的邮箱，起标识作用
   #可自定义密钥位置及名称。
   #密钥路径以`/`开头。密钥名称为不包括`.pub`的名称
   ```

   > `git-bash`中，路径以`/`开头，如D：盘为`/d/`。可参考图片

   {% asset_img 1582557464874.png 自定义密钥位置 %}

4. 添加密钥

   ```sh
   ssh-agent bash #直接在bash执行下一条命令连接不到认证服务器
   ssh-add /path/.ssh/name #无`.pub`后缀
   ```

5. 创建config文件。必须是修改“~/.ssh/config”文件。其余地方的不生效

   ```sh
   vi ~/.ssh/config
   ################start
   Host walk143 -- git@walk143:walk143/mavenssm.git中的walk143
   User walk143 -- 未知
   Hostname  github.com --实际域名，或为gitee.com 一般不可更改
   PreferredAuthentications publickey
   IdentityFile D:\path\.ssh\name --此处路径为win，可在资源管理器复制
   ##################end
   ```

   > gitee配置文件如下

   {% asset_img 1581556498915.png gitee相关config配置 %}

6. 添加公钥到服务器

   复制生成的公钥

   ```sh
   cat /d/path/.ssh/name.pub
   #复制输出内容到服务器。github.com/gitee.com
   ```

   > gitee在 安全设置>SSH公钥 添加

   {% asset_img 1582558271373.png gitee公钥添加位置 %}
   
7. 配置全局git用户名

   ```sh
   git config --global set user.name "name"
   git config --global set user.email "email@email.com"
   ```


## 远程仓库连接

前提条件：本地新建了一个仓库

1. 服务器初始化一个空仓库

   如`gitee.com`新建一个空仓库，复制仓库ssh链接地址。

2. 本地新建仓库

   ```sh
   git init #初始当前目录为git仓库
   git add . #添加所有文件
   git commit -m "提交信息" #进行一次提交
   ```

3. 关联远程仓库

   ```sh
   ###本地执行
   git remote add origin git@gitee.com:sloera/vue.git
   #其中 origin为后面地址的别名 git@gitee.com:sloera/vue.git为上述仓库ssh地址
   
   git branch --set-upstream-to=origin/master master #当服务器新建为空仓库时，由于尚未生成分支，此条语句不可执行。使用以下命令将本地仓库推送到服务器
   #其中 origin/master 代表上述添加别名仓库的master分支 最后的master代表关联到本地的master分支
   
   git push --set-upstream origin master
   #关联当前分支到origin 的 master分支
   git push 
   #将本地修改推送到远程服务器
   ```
   
4. 本地与远程分支名不同设置

   ```sh
   git config --local/global push.default upstream #指定可以推送到远程不同名分支
   git branch -u origin/source #指定当前分支跟踪远程source分支
   git push #即可将本地的当前分支(如master)推送到远程的source分支
   ```

## 迁移git密钥

将win下的密钥，同步到wsl子系统中

1. 复制密钥

   将win下生成的密钥复制到wsl的`root/.ssh`目录下

2. 复制`config`配置文件

   修改其中`IdentityFile`的路径，改为wsl自己的路径

## 部分git操作

### 全局设置

#### 取消全局username，email

```sh
git config --global --unset user.name
git config --global --unset user.email
```

#### 在repo中设置用户

```sh
git config user.name "name"
git config user.email "name@email.com"
```

全局用户默认“name/name@email.com"

```sh
git config --global user.name "name" 
git config --global user.email "name@email.com"
```

#### 添加远程仓库

```sh
git remote add origin git@github.com:Nickerg/Note.git
git branch --set-upstream-to=origin/master master
#其中 origin/master 代表添加别名仓库的master分支 最后的master代表关联到本地的master分支
git push --set-upstream origin master
```

#### 设置win/linux换行符转换

一般禁止转换回车换行符。

```sh
#后面无true或input或false时，为查看当前配置
#提交时转换为LF，检出时转换为CRLF
git config --global core.autocrlf true
#提交时转换为LF，检出时不转换
git config --global core.autocrlf input
#提交检出均不转换
git config --global core.autocrlf false
```

### .gitignore文件

gitignore只能忽略未跟踪文件，对于已跟踪文件，需要根据`取消跟踪已commit文件`操作

配置语法

- 以 `/` 开头表示根目录,防止递归
- 以 `/` 结尾表示指定目录
- 以 `!` 开头表示不过滤（跟踪）此项配置匹配到的文件或目录
- 以 `#` 开头表示注释，如需转义在前面加斜杠，`/#`

可使用如下通配符

```
* 通配符，多字符通配
**表示匹配任意中间目录如，a/**/z 表示可以匹配a/z、a/s/z或 a/a/s/z 等
? 通配符，单字符通配
[] 可以匹配任何一个在方括号中的字符, 如*.[ac] 表示匹配任何以 .a 或者 .c 结尾的文件，如果[]中有短划线 - 分割两个字符，则表示所有两个字符范围内的都可以匹配如 [0-9]
```

### 取消跟踪已commit文件

对某个文件取消跟踪

```sh
git rm --cached readme1.txt    #删除readme1.txt的跟踪，并保留在本地。
git rm --f readme1.txt    #删除readme1.txt的跟踪，并且删除本地文件。

git rm -r --cached themes/landscape/* #递归目录取消跟踪目录下所有文件
```

### 修改git默认的编辑器

```sh
git config --global core.editor vi
```

