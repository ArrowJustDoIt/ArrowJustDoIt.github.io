---
layout: post
title: Cookie禁用了，Session还能用吗？
category: 技术
tags: php
keywords: 
description: 
---

很必要的一个问题,因为有一部分童鞋觉得cookie不安全,于是就禁用cookie,结果session就哭了-,-
***

###session哭了

　　Cookie与 Session，一般认为是两个独立的东西，Session采用的是在服务器端保持状态的方案，而Cookie采用的是在客户端保持状态的方案。但为什么禁用Cookie就不能得到Session呢？因为Session是用`Session ID`来确定当前对话所对应的服务器Session，而`Session ID是通过Cookie来传递`的，禁用Cookie相当于失去了Session ID，也就得不到Session了。

###session不哭,站起来撸

　　Session，储存于服务器端（默认以文件方式存储Session），`根据客户端提供的Session ID来得到用户的文件，取得变量的值`，Session ID可以使用客户端的Cookie或者Http1.1协议的Query_String（就是访问的URL的“?”后面的部分）来传送给服务器，然后服务器读取Session的目录……。也就是说，`Session ID是取得存储在服务上的Session变量的身份证`。

　　当代码session_start();运行的时候，就在服务器上`产生了一个Session文件，随之也产生了与之唯一对应的一个Session ID`，定义Session变量以一定形式存储刚才产生的Session文件。通过Session ID，可以取出定义的变量。跨页后，为了使用Session，你必须又执行session_start();将`又会产生一个Session文件，与之对应产生相应的Session ID`，用这个session id是取不出前面提到的第一个Session文件中的变量的，因为这个Session ID不是打开它的“钥匙”。`如果在session_start();之前加代码session_id($session id);将不产生新的Session文件，直接读取与这个id对应的Session文件`。

我们可以抛开Cookie使用Session，即假定用户关闭Cookie的情况下使用Session，其实现途径有以下几种：

- 设置php.ini配置文件中的`session.use_trans_sid = 1`，或者编译时打开打开了`--enable-trans-sid`选项，让PHP自动跨页传递Session ID。

{% highlight php linenos %}
-------------------------------------------------------------------------------------------------------------------
       <?php
           // s1.php
           session_start();
           $_SESSION['lalala']="papapa";
           $url="<a href='s2.php'>下一页</a>";
           echo $url;
       ?>
       -------------------------------------------------------------------------------------------------------------------

       -------------------------------------------------------------------------------------------------------------------
       <?php
           // s2.php
           session_start();
           echo $_SESSION['lalala'];
       ?>
       -------------------------------------------------------------------------------------------------------------------
{% endhighlight %}

运行以上代码，在客户端cookie正常的情况下，应该可以得到结果"papapa"。
现在你手动关闭客户端的cookie，再运行，可能得不到结果了吧。如果得不到结果，再设置php.ini文件中的`session.use_trans_sid = 1`，或者编译时打开打开了`--enable-trans-sid选项`，又得到结果“papapa”。 

- 手动通过URL传值、隐藏表单传递Session ID。

{% highlight php linenos %}
-------------------------------------------------------------------------------------------------------------------
       <?php
           // s1.php
           session_start();
           $_SESSION['lalala']="papapa";
           $sn = session_id();
           $url="<a href='s2.php?sn=".$sn."'>下一页</a>";
           echo $url;
       ?>
       -------------------------------------------------------------------------------------------------------------------

       -------------------------------------------------------------------------------------------------------------------
       <?php
           // s2.php
           session_id($_GET['sn']);
           session_start();
           echo $_SESSION['lalala'];
       ?> 
       -------------------------------------------------------------------------------------------------------------------
{% endhighlight %}

- 用文件、数据库等形式保存Session ID，在跨页过程中手动调用。

{% highlight php linenos %}
-------------------------------------------------------------------------------------------------------------------
       login.html
       <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"> 
       <html> 
       <head> 
       <title>Login</title> 
       <meta http-equiv="Content-Type" content="text/html; charset=gb2312">
       </head> 
       <body> 
       请登录： 
       <form name="login" method="post" action="mylogin1.php"> 
       用户名:<input type="text" name="name"><br> 
       口　令:<input type="password" name="pass"><br> 
       <input type="submit" value="登录"> 
       </form> 
       </body> 
       </html> 
       -------------------------------------------------------------------------------------------------------------------

       -------------------------------------------------------------------------------------------------------------------
       mylogin1.php
       <?php
       $name=$_POST[’name’];
       $pass=$_POST[’pass’];
       if(!$name || !$pass) {
           echo "用户名或密码为空，请<a href='login.html'>重新登录</a>";
           die();
       }
       if (!($name=="lalala" && $pass=="papapa") {
           echo "用户名或密码不正确，请<a href='login.html'>重新登录</a>"; #随便测试写,不要注意这些细节-,-
           die();
       }
       //注册用户
       ob_start();								#ob缓存
       session_start();							#session开启
       $_SESSION[’user’]= $name;				#设置session
       $psid=session_id();						#保存session id
       $fp=fopen("D:\tmp\phpsid.txt","w+");		#打开文件
       fwrite($fp,$psid);						#把session id写入文件
       fclose($fp);								#关闭文件
       //身份验证成功，进行相关操作
       echo "已登录<br>";
       echo "<a href='mylogin2.php'>下一页</a>";
       ?>
       -------------------------------------------------------------------------------------------------------------------

       -------------------------------------------------------------------------------------------------------------------
       mylogin2.php
       <?php
       $fp=fopen("D:\tmp\phpsid.txt","r");		#打开文件
       $sid=fread($fp,1024);					#取出session id
       fclose($fp);								#关闭文件
       session_id($sid);						#设置session id
       session_start();							#开启session
       if(isset($_SESSION[’user’]) && $_SESSION[’user’]="laigw" {
           echo "已登录!";
       } else {
           //成功登录进行相关操作
           echo "未登录，无权访问";
           echo "请<a href="login.html">登录</a>后浏览";
           die();
       }
       ?>
       -------------------------------------------------------------------------------------------------------------------
{% endhighlight %}

同样请关闭Cookie测试，用户名：lalala；密码：lalala；这是通过文件保存Session ID的，文件是：D:/tmp/phpsid.txt，请根据自己的系统决定文件名或路径。 

总结一下，上面的方法有一个共同点，就是在前一页取得Session ID，然后想办法传递到下一页，在下一页的session_start();代码之前加代码Session ID(传过来的Session ID)。

打完收工:p