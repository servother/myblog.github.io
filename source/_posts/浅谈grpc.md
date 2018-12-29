---
title: 浅谈grpc
date: 2018-12-29 11:50:50
tags: grpc
categories: golang
---
# 浅谈grpc
## grpc的实现流程
### 概念
1.简介：rpc即远程过程调用，简单来说就是两个进程间的通讯。grpc就是实现rpc的一个框架，但是摊上了一个好爹--google。
一个完整的grpc包含以下流程：
图文（略）
概括来说，grpc的流程为：
1. 通过.proto文件定义传输的接口和消息体。
2. 通过protocol编译器生成server端和client端的stub程序。
3. 将请求封装成HTTP2的stream。
4. 通过channel作为数据通信通道使用socket进行数据传输。

<!-- more -->
未完（留坑）