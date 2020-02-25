---
title: gitee-hexo搭建个人博客
date: 2020-02-25 18:17:34
tags:
---
参考地址：<https://blog.csdn.net/cungudafa/article/details/104260494>

### 工具

- node.js

- git

  配置见{% post_link git "git配置文章" %}

> 已安装好上述工具及hexo-cli.
>
> `npm install hexo-cli -g`

### hexo博客模版

1. 新建`root`目录。存储所有markdown文件及生成的html

   ```sh
   $ pwd
   /j/data/gitee
   $ mkdir sloera
   $ cd sloera/
   $ npm -v
   6.13.4
   ```

2. 安装相关依赖

   ```sh
   cnpm install -g hexo-cli
   cnpm install hexo-deployer-git --save
   ```

   

3. 初始化

   ```sh
   hexo init #下载主题耗时
   #卡到安装依赖时 Ctrl+C
   cnpm install
   ```

4. 启动测试

   ```sh
   hexo clean
   hexo g
   hexo s
   #测试三连
   hexo clean && hexo g && hexo s
   ```

   

5. gitee配置

   1. 新建一个仓库名与码云登录名一致的仓库

      选择开源：公开；语言：JavaScript

6. hexo配置

   1. 修改hexo配置

      修改`_config.yml`

      ```yaml
      deploy:
        type: git
        repo: git@gitee.com:sloera/sloera.git
        branch: master
      ###注意`:`后有空格
      ```

   2. 部署

      ```sh
      hexo g --d #一键部署
      ```

   3. 开启giteePage功能

      {% asset_img 1582550759765.png "gitee Pages功能" %}

   4. 与typora编辑匹配图片

      1. 首先确认**_config.yml**中有**post_asset_folder:true**。

      2. 开启后，将图片放入文件名同名目录中即可引用。

      3. 修改typora图片复制操作，用以匹配`post_asset_folder`

         {% asset_img 1582551993923.png Typora图片设置 %}

      4. 安装 hexo-asset-image

         ```sh
         cnpm install https://github.com/CodeFalling/hexo-asset-image --save
         ```

7. 主题配置

   ```sh
   cd sloera
   git clone https://github.com/theme-next/hexo-theme-next themes/next
   ```

### hexo相关操作

1. 相对路径引用的标签插件

   ```md
   {% post_link 文章文件名（不要后缀） 文章标题（可选） %}
   {% asset_path slug %}
   {% asset_img slug [title] %}
   {% asset_link slug [title] %}
   #如 配置见{% post_link git配置 %}
   ```

   正确的引用图片方式是使用下列的标签插件而不是 markdown ：

   ```
   {% asset_img 1582550759765.png "gitee Pages功能" %}
   ```

2. 引用自定义文件

   将文件复制到文件同名目录下。以如下方式引用

   ```
   {% asset_link PortableGit-2.23.0-64-bit.7z.exe PortableGit-2.23.0-64-bit.7z.exe %}
   ```
   
3. 管理博客源码

   1. 创建一个`source`分支存放源码，并更改为默认分支

      {% asset_img 1582630764261.png 设置默认分支 %}

      在`管理>基本设置>默认分支`中切换为新建的分支

   2. 初始化仓库，添加远程
   
      ```sh
      git init
      git remote add origin git@gitee.com:sloera/sloera.git
      git add .
      git commit -m "博客源文章"
      git push -f origin master:source #首次为从master创建分支，包含静态网页，需要使用-f强制覆盖
      #master代表本地分支，source代表远程分支名
      ```

   3. 禁止转换回车换行符
   
      ```sh
      git config core.autocrlf #查看当前配置
      git config core.autocrlf false #将设置改为false
      ```
      
   4. 默认跟踪远程source分支
   
      ```sh
      git branch -u origin/source #指定当前分支跟踪远程source分支
      git config --local push.default upstream #指定可以推送到远程不同名分支
      git add .
      git commit -m "默认分支测试"
      git push
      ```