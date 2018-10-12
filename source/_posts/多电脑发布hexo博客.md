---
title: 多电脑发布hexo博客
tags: 随笔
categories: 随笔
---
# 多电脑发布hexo博客

## 准备github账号
在[github](http://github.com)上注册账号，并新建仓库。

## 安装nodejs
按照网上的教程安装nodejs和hexo，教程很多不赘述了。

## github上新建分支，将部署源码提交上去
在博客库新建分支xxx，例如笔者命名为hexo分支，将部署源码提交上去。这里需要修改.gitignore文件，将不必要提交的本地配置等文件加进去。

## 另一台电脑安装hexo，clone部署源码
另一台电脑安装和部署好hexo的环境，配置好Git，clone部署源码，就可以协同发布了。

#总结
现在一个电脑上提交部署源码到git上，后面就是在git上协同发布博客的操作。
电脑A
``` bash
git add .
git commit -m "提交部署源码"
git push origin hexo   //初次将部署源码提交上hexo分支
```

电脑B
``` bash
git pull   //先更新下本地内容，再修改，再提交
git add .
git commit -m ""
git push origin hexo
```

电脑A
``` bash
git pull   //先更新下本地内容，再修改，再提交
git add .
git commit -m ""
git push origin hexo
```