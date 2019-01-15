---
title: 浅谈mysql中的binlog
date: 2019-01-15 14:47:06
tags: mysql
categories: mysql
---
## 浅谈mysql中的binlog
### binlog是什么？
binlog是mysql中的二进制日志，可以说是mysql中最重要的日志。它记录了几乎所有的DDL和DML语句（除了select查询语句），以事件形式记录，还包含语句执行所消耗的时间。mysql的二进制日志是事务安全型的。
1. DDL即数据库定义语言。主要命令有create，alter，drop等，DDL主要是用在定义或改变表的结构，数据类型，表之间的链接和约束等初始化工作上。
2. DML即数据库操纵语言。主要命令有select，update，delete，insert等，主要是进行表数据的操作工作。

### binlog有什么作用？
最常用的就是两个作用：一是mysql的主从复制，将主库的binlog日志传递到从库来更新数据从而实现主从复制；二是mysql的数据恢复。
<!-- more -->

### 如何使用binlog？
#### binlog日志包括两类文件：
1. 二进制日志索引文件（文件名后缀为.index）用于记录所有的二进制文件。
2. 二进制日志文件（文件名后缀为.00000*）记录数据库所有的DDL和DML（除select）语句事件。
	
#### 开启binlog日志功能
1. 编辑打开mysql配置文件etc/mys.cnf
2. 在[mysqld]区块添加`log-bin=mysql-bin`，确认是打开状态
3. 重启mysql服务使配置生效
4. 查看binlog是否开启，使用`show variables like 'log_%';`，打开状态时结果集中会出现log_bin = ON。
	
#### 常用的binlog日志操作命令
1. 查看所有binlog日志列表，命令：`show master logs;`
2. 查看Master状态，即最新一个binlog日志的编号名称，以及最后一个操作事件pos结束点值，，命令：`show master status;`
3. flush刷新log日志，自此刻开始产生一个新编号的binlog日志文件，命令：`flush logs;`。另外，当mysql服务重启时会自动触发这个命令，还有mysql dump备份数据的时候带上了参数-F也会触发。
4. 清空所有binlog日志，命令：`reset master;`
	
#### 如何查看binlog日志
1. 使用mysqlbinlog自带命令查看，例如`mysqlbinlog mysql-bin.000002`，使用cat,more,vim等命令是无法查看二进制日志文件的。
2. 上述方法将整个binlog日志文件内容读出，内容过多极不容易查看。可以使用`show binlog events[IN 'log_name'][from pos][LIMIT [offset,] row_count];`
	IN 'log_name'：指定要查询的binlog文件名（不指定就是从第一个binlog文件）
	from pos：指定从哪个pos起始点查起（不指定就是从整个文件的第一个pos点起）
	LIMIT [offset,]：偏移量（不指定则为0）
	row_count：查询总条数（不指定就是所有行）
	
#### 利用binlog日志恢复mysql数据
世上没有后悔药，即使有也肯定不好吃！所以恢复mysql的数据，就是在你开启了mysql的binlog日志功能后，通过阅读binlog日志文件的内容（重新去执行特定区间段的SQL语句）去挽回你的错误。   
温馨提醒：恢复数据之前必须将binlog移出，否则恢复过程中会继续写入语句到binlog中，容易造成混乱。

