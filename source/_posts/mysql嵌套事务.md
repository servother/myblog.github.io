---
title: mysql嵌套事务的隐式提交
date: 2018-10-08 16:15:27
tags: mysql
categories: mysql
---
mysql官方文档是表明不支持嵌套事务的。在一个事务中开启另一个事务，会隐式commit外层事务。
## 示例
新建一个测试表（建表SQL略），表名：demo，字段：id（自增），txt。开启事务如下：
``` bash
START TRANSACTION;
INSERT INTO demo (txt) VALUES ('job');
START TRANSACTION;
INSERT INTO demo (txt) VALUES ('cook');
COMMIT;
ROLLBACK;
```
当你只执行前两句SQL时，此时观察表中并没有txt为job的数据。当你执行前三句你会发现表中已经有了txt为job的数据。
<!-- more -->
## 相关
还有以下一些关键词会触发mysql的隐式提交。
``` bash
ALTER FUNCTION    
ALTER PROCEDURE    
ALTER TABLE    
BEGIN    
CREATE DATABASE    
CREATE FUNCTION    
CREATE INDEX    
CREATE PROCEDURE    
CREATE TABLE    
DROP DATABASE    
DROP FUNCTION    
DROP INDEX    
DROP PROCEDURE    
DROP TABLE    
UNLOCK TABLES    
LOAD MASTER DATA    
LOCK TABLES    
RENAME TABLE    
TRUNCATE TABLE    
SET AUTOCOMMIT=1    
START TRANSACTION
```