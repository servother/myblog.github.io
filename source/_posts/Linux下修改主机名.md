---
title: Linux下修改主机名
date: 2018-10-18 16:07:14
tags: Linux
categories: Linux
---
# LInux下修改主机名

## hostname + 主机名
这种修改只是暂时有效，在当前Linux运行期间有效。重启后就失效了。

## 修改/etc/hosts
这种方法文件里的主机名只是用来提供给dns解析的.如果你用不上dns,只需要修改主机名,那修改这个没用。

## 修改这个文件etc/sysconfig/network里的主机名
这种方法可以实现永久修改主机名，需要重启Linux后才能生效。
``` bash
NETWORKING=yes
HOSTNAME=主机名
```

# 总结：
1. `#hostname 新主机名`
2. 修改/etc/sysconfig/network中的hostname的值
3. 修改/etc/hosts文件