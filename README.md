# 博客源码管理

## hexo环境安装

```sh
cnpm install hexo-cli -g
cnpm install
```

## 常用命令

```sh
hexo clean;hexo g;hexo s;
# 清理内存，生成文件，运行服务

hexo clean;hexo g -d;hexo s;
# 清理内存，生成并部署文件到服务器，运行服务

hexo new title -p /path/title
#指定新文章生成的目录，需要写两次title。默认是pages页面

hexo d; # 单独部署到服务器命令
```