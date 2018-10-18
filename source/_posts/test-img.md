---
title: hexo引用图片
date: 2018-10-18 17:20:28
tags: 编程
categories: 编程
---
# hexo引用图片
## 安装插件
`npm install https://github.com/CodeFalling/hexo-asset-image --save`
## 使用语法
跟正常的Markdown引用图片一样，只是路径使用的是相对路径。在你安装插件之后，使用`hexo new "新博客"`生成新博客时。  
_post文件夹下会有个跟新博客同名的文件夹，该文件夹下就是用来放置你引用的图片的。  
在博客中你可以这样使用`xxxx![图片名称](文件夹名称/图片名称 "图片标题")xxxx`，xxxx指的是你的博客内容，
图片名称就是显示在图片下面的名称，图片标题就是鼠标放上去显示的文字。  
例如我使用`![我是图片](test-img/1.jpg "测试图片")`，实现效果为：  
![我是图片](test-img/1.jpg "测试图片")
