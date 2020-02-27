---
title: git
date: 2020-02-25 23:03:38
tags: 
    - git
    - 工具
    - 配置
---

## git安装

1. 下载git

   <https://git-scm.com/download/win>

   可下载portable版本。 {% asset_link PortableGit-2.23.0-64-bit.7z.exe [PortableGit-2.23.0-64-bit.7z.exe] %}

2. 解压到自定义目录

   添加`自定义目录/bin`到`系统变量>Path`

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

### 部分git操作

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
   git push --set-upstream origin master
   ```

   