---
title: hexo简单设置
tags:
  - hexo
  - 配置
  - 工具
date: 2020-02-27 21:40:14
categories:
   - 工具
   - 服务
   - hexo
---


## 设置tags

1. 新建tags页面

   在`source`路径下新建存储tags的文件夹。

    ```sh
    hexo new page "tags"
    ```
   
2. 编辑`tags`文件夹下的`index.md`文件

   在头部加入`type: "tags"`

   {% asset_img 1582809658520.png tags文件设置%}

3. 在主题配置文件夹中，添加菜单链接

   编辑主题配置文件`themes/next/_config.yml`，取消默认`tags`的注释

   ```yaml
   menu:
     home: / || home   # 主页链接及其图标
     # about: /about/ || user   # 关于页链接及其图标
     tags: /tags/ || tags      # 标签页链接及其图标
     # categories: /categories/ || th   # 分类页链接及其图标
     archives: /archives/ || archive   # 归档页及其图标
     # schedule: /schedule/ || calendar
     # sitemap: /sitemap.xml || sitemap
     # commonweal: /404/ || heartbeat
   ```

   注：`||`后的为[fontawesome](http://fontawesome.dashgame.com/)的图标名。

   {% asset_img 1582809796182.png 开放菜单设置%}

## 设置分类

1. 文章中使用

   在头部添加如下信息进行分类
   ```yaml
   categories:
      - 工具
      - 服务
      - hexo
   ```
   分类与标签不同，分类有顺序之分，即工具>服务>hexo。

2. 新建存储菜单

   ```sh
   hexo new page "categories"
   ```
   编辑生成文件夹下的`index.md`，添加`type: "categories"`
   
   注：`type的属性值必须小写`，猜测为和内部属性绑定。

3. 配置文件夹中添加菜单链接

## 编辑草稿

1. 新建草稿

   ```sh
   hexo new draft hexo简单设置
   ```

   会在`source/_drafts`下生成文件。

   {% asset_img 1582810734984.png 草稿文件%}

   编辑方式同正常文件，只是发布时不会显示。

   

2. 预览草稿

   ```sh
   hexo s --draft #启动服务的时候，加上draft参数即可。
   #######
   #或者也可以修改配置文件
   render_drafts: true
   ```

3. 将草稿变为正文

   ```sh
   hexo publich hexo简单设置
   ```
   相比于新建文件，头部信息缺少`date`信息。