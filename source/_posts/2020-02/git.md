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
   cat /d/path/.ssh/name
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

## 部分git操作

1. 取消全局username，email

   ```sh
   git config --global --unset user.name
   git config --global --unset user.email
   ```

2. 在repo中设置用户

   ```sh
   git config user.name "name"
   git config user.email "name@email.com"
   ```

   全局用户默认“name/name@email.com"

   ```sh
   git config --global user.name "name" 
   git config --global user.email "name@email.com"
   ```

3. 添加远程仓库

   ```sh
   git remote add origin git@github.com:Nickerg/Note.git
   git branch --set-upstream-to=origin/master master
   #其中 origin/master 代表添加别名仓库的master分支 最后的master代表关联到本地的master分支
   git push --set-upstream origin master
```
   
   