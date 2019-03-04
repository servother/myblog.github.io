---
title: PHP面试题记录
date: 2019-03-04 15:41:03
tags: PHP
categories: PHP
---
## 1.如何获取用户真实IP？
	PHP中getenv("REMOTE_ADDR")用来获取用户的IP，但用户使用代理服务器后，得到的会是代理服务器的IP地址；此时可以用getenv("HTTP_X_FORWARDED_FOR")来获取，但是当用户没有使用代理时会返回空值（返回的可能是多个ip地址，所以能够判断用户是否使用代理ip）。

## 2.使用PHP实现快速排序算法？
	```
	留坑
	```
	
## 3.PHP中的魔术方法及作用？
	__construct：构造方法，实例对象时调用。
	__destrcut：析构方法，在对象被销毁时调用。
	__set：设置一个不能访问的成员属性时触发。
	__get：获取一个不能访问的成员属性时触发。
	__clone：在创建一个新对象时触发。
	__call：访问一个不能访问的成员方法时触发。
	__callStatic：访问一个不能访问的静态成员方法时触发。
	__toString：当对象被当作字符串处理时触发。
	__invoke：当以函数的方式调用对象时触发。
	__sleep：在对一个对象的数据处理，不需要保存全部数据时触发。比如serialize()序列化时
	__wakeup：在unserialize()时，会先检查是否有__wakeup()方法。
	__unset：在一个不能访问的对象属性被unset()时触发。
	__isset：在一个不能访问的对象属性被isset(),empty()时触发。
	__debugInfo：在一个对象被var_dump时触发。
	__set_state：在一个对象被var_export时触发。
	
## 4.
	