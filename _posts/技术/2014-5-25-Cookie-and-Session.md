---
layout: post
title: Cookie与Session
category: 技术
tags: php
keywords: 
description: 
---

最近忙着期末考试,也没有什么时间研究技术了-,- 所以自己整理下平时对session和cookie的理解
***

###**会话技术**
**会话技术**: 在一次浏览器打开后,多次访问同一个网站的时候, 都认为当前浏览器是已经访问网站的(服务器记住浏览器): 从而实现跨脚本的共享数据.

会话技术包含两种: cookie和session技术

**Cookie技术**: 
是一种服务器通过http协议将数据保存到浏览器端, 然后浏览器又通过http协议将数据携带给服务器的技术. 从而实现服务器识别浏览器的功能.

**Session技术**: 
session是一种将数据保存在服务器端的技术, 通过将”信物”通过http协议(cookie)保存到浏览器端, 浏览器通过cookie将”信物”携带给服务器, 服务器通过”信服”找到对应的数据, 从而实现跨脚本共享数据.

###**Cookie技术**

Cookie: 服务器将数据通过HTTP协议保存到浏览器, 然后浏览器在下次访问(HTTP协议)的时候将数据携带给服务器, 实现服务器识别浏览器的目的(数据共享).

Cookie技术是会话技术的鼻祖

Cookie技术实现是基于HTTP协议中的两个协议项:

- 响应头: set-cookie ,服务器设置数据给浏览器

- 请求头: cookie, 浏览器携带数据给服务器

###如何设置cookie

- **header实现Cookie**:修改响应头将数据传递给浏览器

```
Header(‘set-cookie: 名字 = 值’);
```
- **Setcookie函数**:一个专门用于设置cookie的函数

```
Setcookie(名字,值，参数3，参数4，参数5);
Setcookie('age',18);	                       //参数1：cookie的名字。参数2：cookie的值
Setcookie('age',18,time()+10);	               //参数3：Cookie生命周期-->当前时间再过10秒后失效
Setcookie('age',18,time()+10,'/')；            //参数4：Cookie作用范围-->‘/’表示整个网站下都能访问
Setcookie('age',18,time()+10,'/','lalala.com');//参数5：Cookie跨域-->默认的,cookie是不能跨域的,这样设置的话只要以lalala.com结束的域名都可以访问
```

###如何接收cookie

PHP使用一个超全局预定义变量$_COOKIE自动接收所有的cookie数据

要想接收数组可以通过设置cookie的时候借助于[]实现

###Cookie总结
1.	Cookie不安全: 谁都可以访问(js),通常不会使用cookie来保存重要的数据
2.	Cookie适合保存不重要而且数据量大的东西.(视频网站)
3.	Cookie保存在浏览器端

###**Session技术**

Session: 将数据保存在服务器端的技术. 是一种将数据保存到服务器上的文件中.

Session技术是基于cookie技术实现.

Session是通过产生sessionID(cookie), 去创建对应的文件,然后将数据保存到文件中, 将sessionid发送给浏览器. 浏览器携带sessionid,服务器能够接收id并且找到文件取出数据.

核心的工作: 都是由session_start()完成

###如何设置Session

Session技术有两种实现方式(类似mysql中的事务处理): `手动session`和`自动session`

Session在PHP系统中有一套完整的机制(用户不需要管内部实现), 对外提供了一些可以操作的接口(应用接口: 函数)

###手动session

- 激活session系统: 让session系统开始工作

```
Session_start(): #开启session机制, 任何要使用session之前都必须开启session机制
```
PHPSESSID这个cookie由session_start()函数产生,就是session数据的”钥匙”

- 存储数据到session中: 使用$_SESSION存储数据

```
Session_start();
$_SESSION[‘name’] = ‘mark’;
```

- 在新的脚本中读取session数据: 任何想使用session的时候都必须开启session

```
Session_start();
Var_dump($_SESSION);
```

###自动session

自动session: session机制一直是打开的,根本不需要用户去开启session.

用户要做的事情是: 存储数据($_SESSION),使用数据($_SESSION)

要实现自动session: 必须修改配置文件自动开启
 
```
;session自动开启
;session.auto_start = 1
```

通常不会使用自动session.=.=

###如何删除Session

删除session文件: 在开启session之后,session_destroy();

```
Session_start();
Session_destroy();
```

Session文件的删除是根据sessionID删除: 一个浏览器的一次请求永远只能删除对应的sessionid的session文件.

###如何配置Session

Session数据的存储方式: 默认是文件

```
修改session数据的存储方式-->存储到文件里面
session.save_handler = files
```

Session的存储路径: 默认的是操作系统的临时文件夹

```
;设置session数据的存储位置-->存到文件中
session.save_path = "F:\Program Files (x86)\server\TEMP"
```

Session是基于cookie: 使用cookie保存sessionid

```
session.use_cookies = 1
```

Session只允许使用cookie保存sessionid

```
session.use_only_cookies = 1
```

Session名称: cookie中sessionid的名字

```
session.name = PHPSESSID
```

Session默认不是自动开启

```
session.auto_start = 0
```

Session过期: 因为sessionid的cookie生命周期: 浏览器关闭

```
session.cookie_lifetime = 0
```

Sessionid的cookie作用范围是整站共享

```
session.cookie_path = /
```
Session数据保存是使用php自己的序列化方式(序列化不是对$_SESSION序列化,而是对$_SESSION里面的数据进行序列[下标不序列化])

```
session.serialize_handler = php
```

Session垃圾回收机制

```
session.gc_probability = 1
session.gc_divisor = 1000			#session_start开启1000次有一次可能触发垃圾回收机制,一次性回收所有垃圾文件
session.gc_maxlifetime = 1440		#session文件的生命周期是24分钟
```

Session默认不允许将sessionid存储到a标签里面的href中

```
session.use_trans_sid = 0
```

###Session分层

有可能网站的瞬时访问量很大, 会产生很多的session文件: 因为操作系统寻找文件是逐个匹配: 效率特别低.

- 解决方案1: 将session文件放到不同的文件夹下: 以sessionID的首字母为文件夹
 

`使用分层的缺点1`: 需要手动创建文件夹(系统不会自动创建)
 
为了保证安全性: 必须手动创建36个(26个字母+10个数字)文件夹,如果分成两层: 36 * 36个文件

`使用分层的缺点2`: 文件读取有i/o限制(并发问题)


- 解决方案2:将session保存到数据库(数据库的并发量比文件要高的多:  数据库可以是非关系型数据库[内存])

打完收工:p