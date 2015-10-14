---
layout: post
title: Mysql主从复制
category: 技术
tags: mysql
keywords: 
description: 
---

为了减少数据库的压力,我们通常配置多个数据库,把增删改语句放在主数据库中操作,查询放在从数据库中操作

###**什么是主从复制**


###**主从复制实现原理**

**原理**:mysql有一种日志,叫做`log-bin`日志(二进制日志),它会记录下所有修改过数据库的sql语句,**主从复制**实际上是多台服务器都开启log-bin日志,主服务器会把sql语句保存到log-bin日志中,主服务器更新log-bin日志后会告知从服务器,从服务器通过主服务器grant授权的用户查询主服务器的log-bin日志,并把日志文件里的sql语句执行一遍,则实现了数据同步


###**如何授权一个用户**

####`mysql添加账号`

语法：grant 权限  on  指定数据库和表   to “用户名”@’可以登录主机的ip地址’  identified by ‘密码’

```
grant all  on *.* to ‘lalala’@’%’  identified by ‘papapa’;
```
####`mysql删除账号`
语法：drop user ‘用户名’@’授权登录主机的ip地址’

```
drop user ‘lalala’@’%’
```
###**log-bin日志**

####`1 开启log-bin日志`

打开mysql的配置文件：my.ini(window)     my.cnf  (linux)

```
# 数据库主从复制,mysql-bin 是二进制日志文件的名称,默认是存储数据库文件同一级目录,可以自己指定,比如log-bin=f:/lalala.txt
log-bin=mysql-bin
```
之后重启mysql服务，会产生新的一个日志文件(`每次重启都会产生新的`)

####`2 和log-bin日志相关的几个函数`

 1. reset master  
清空所有的 log-bin日志，并产生一个新的log-bin日志文件。

 2.  flush logs  
产生新的一个log-bin日志文件。

 3. show master status  
 查看最新的一个log-bin日志的文件名称，并包含pos位置(`记录sql语句在log-bin日志文件中的位置`)

![1](/public/img/technology/Replication.png)

上图的数字就是pos代表的位置

####`3 查看log-bin日志文件里面的内容`

使用在mysql的安装目录的bin目录下面的mysqlbinlog.exe命令来完成查看

```
mysqlbinlog   --no-defaults    日志文件的名称（包含全路径）
```

####`4 完成log-bin日志恢复数据`

只需要把日志文件里面的sql语句执行一遍，即可完成数据恢复

```
mysqlbinlog --no-defaults 日志文件的名称（路径）| mysql –u用户名 –p密码 数据库的名称
```

如果想恢复具体位置的sql数据,只需查找记录增删改sql语句在log-bin日志文件中的pos 开始位置和结束位置

```
mysqlbinlog --no-defaults --start-position=”开始位置” --stop-position=”结束的位置” 日志文件的名称（路径）| mysql –u用户名 –p密码 数据库的名称 
```

###**主从配置**
###主服务器配置流程
####`1 修改my.ini,添加如下语句`

```
log-bin=mysql-bin

server-id=1
```

####`2 重启mysql服务器`
####`3 授权从服务器读取log-bin日志信息用户`

```
grant replication slave on *.* to 'lalala'@'%' identified by 'lalala';#replication slave 分配复制的权限
```
####`4 查看主服务器最新的log-bin日志信息`

```
Show master status;
```

###从服务器配置流程

####`1 修改my.ini,增加如下配置：`

```
log-bin=mysql-bin	#二进制日志文件
server-id=2			#每台服务器id不能一样
```

####`2 重启mysql服务器`
####`3 进入到mysql命令行后执行下面命令：`

```
	stop slave;			#关闭从服务
	reset slave all;		#删除从服务器的配置
	change master to master_host="192.168.28.65",master_user="lalala",master_password ="lalala" ,master_log_file="mysql-bin.000002",master_log_pos=107； #配置与主服务器的链接
	start slave;			#开启从服务
	show slave status;		#查看主从服务状态是否成功
```

`Slave_IO_Running:Yes`

此进程负责从服务器从主服务器上读取binlog 日志，并写入从服务器上的中继日志

`Slave_SQL_Running:Yes`

此进程负责读取并且执行中继日志中的binlog日志

注：以上两个都为yes则表明成功，只要其中一个进程的状态是no，则表示复制进程停止，错误原因可以从`last_error`字段的值中看到。
通过在主服务器上面创建一个新的数据库，并创建一张新表，并添加记录，查看从服务器是否同步来完成测试。
配置完成后，要禁止对从服务器执行增删改的操作。

###如何撤销从服务器

在从服务器上面执行：

停止从服务器：`stop slave;`

删除从服务器的一些配置：`reset slave all;`

###在项目中如何使用主从配置，完成读写分离

{% highlight php linenos %}
class  mysql{
	$dbm=主服务器 
	$dbs1=从服务器 
	$dbs2=从服务器 
	public function query(){
	#在query里面进行语句判断，分析连接不同的mysql服务器。如果是增删改的操作，就连接主服务器，如果是查询操作，则随机连接从服务器。
	}
} 

{% endhighlight %}

###读写分离在TP框架里面实现

修改TP框架项目的配置文件：

{% highlight php linenos %}
'DB_DEPLOY_TYPE'=>1,//分布式数据库支持
'DB_TYPE'               => 'mysql',     // 数据库类型
'DB_HOST'               => 'localhost,192.168.1.1', // 服务器地址，多个用逗号隔开，默认第一个是主服务器，其余是从服务器。
'DB_NAME'               => 'php,php',          // 数据库名
'DB_USER'               => 'root,lalala',      // 用户名
'DB_PWD'                => 'root,lalala',          // 密码
'DB_PORT'               => '3306',        // 端口
'DB_PREFIX'             => '',
'DB_RW_SEPARATE'=>true,//支持读写分离 
'DB_MASTER_NUM'         =>  1, // 读写分离后 主服务器数量
'DB_SLAVE_NO'       =>  '', // 指定从服务器序号，如果为空，则随机连接从服务器。
{% endhighlight %}
