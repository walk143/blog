# 博客源码管理

## hexo环境安装

```sh
npm install cnpm -g --registry=https://registry.npm.taobao.org
npm config set registry https://registry.npm.taobao.org
cnpm install hexo-cli -g --save
cnpm install
rm -rf themes/next/
git clone https://hub.fastgit.org/next-theme/hexo-theme-next themes/next
```

## 常用命令

```sh
hexo clean;hexo g;hexo s;
# 清理内存，生成文件，运行服务

hexo clean;hexo g -d;hexo s;
# 清理内存，生成并部署文件到服务器，运行服务

hexo new title -p /path/title
#如：hexo new win-jenkins安装 -p 2020-04/win-jenkins安装
#指定新文章生成的目录，需要写两次title。默认是pages页面

hexo d; # 单独部署到服务器命令
```
