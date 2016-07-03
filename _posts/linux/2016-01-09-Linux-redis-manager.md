---
layout: post
title: windows下redis可视化
category: Linux
tags: redis
keywords: 
description: 
---

>本文章只是给对这方面不熟悉的朋友讲解实现过程,其实很简单

##linux安装redis

这个可以查看我之前的文章.

##配置redis

###首先进入到redis的配置文件中

	vi /usr/local/redis/etc/redis.conf

###设置密码

	在命令行模式中 敲击'/',输入'requirepass'按回车,按n查询下一个,当查询到'requirepass foobared'时,在下一行添加'requirepass XXX',其中XXX为进入redis的密码

###设置bind

	和上面一样搜索'bind' ,找到'bind 127.0.0.1',这句配置代码的意思是只允许后接ip地址的访问,所以我们可以选择改为自己的IP地址或者直接#注释,保存退出并重启redis

###安装redisdesktopmanager

[redisdesktopmanager](http://redisdesktop.com/download)下载并安装运行,点击左下角connect to redis server,参数如下

name:随意

host:服务器的ip地址

port:redis默认6379端口

auth:刚才我们设置的密码

点击ok就可以操控我们的redis数据库

###测试
当然为了保险起见,我们要测试redis数据库是否真正的连接成功,打开我们的redis客户端

![1](/public/img/technology/redismanager2.png)

我们可以很明显的看到,在我们修改了密码后,在linux客户端登录要使用-a选项+密码才能正常登陆,也稍微提高了我们数据的安全性

在windows客户端刷新数据库,果然出现了name-zhangsan

打完收工 :p




