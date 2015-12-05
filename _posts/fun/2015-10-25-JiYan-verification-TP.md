---
layout: post
title: 极验验证
category: fun
tags: 验证
keywords: 
description: 
---
> 极验验证是一种在计算机领域用于区分自然人和机器人的，通过简单集成的方式，为开发者提供安全、便捷的云端验证服务。
> 与以往传统验证码不同的是，极验通过分析用户完成拼图过程中的行为特征，通过数据分析来判断是人还是机器。用户不必面对眼花缭乱的英文字符或汉字，整个验证过程变的像游戏一样有趣。
>       --  百度百科

***
安全不敢下定论,倒是挺有趣的,在用户的角度讲,觉得新奇~

---

## 准备


首先到[极验官网](http://www.geetest.com/)注册后台账号,获取id和key

通过链接到GitHub下载[phpsdk](https://github.com/GeeTeam/gt-php-sdk)

## 配置

在tp配置文件config.php设置自定义配置项,也就是id和key

	"CAPTCHA_ID" => "3386e03c620a4067f18fa92c370f1594",
	"PRIVATE_KEY"=> "5fe89444b54d3a3b8e49594c42a770cf",

在登录页面想放置验证码的地方加入

	<div id="div_geetest_lib">
		<div id="div_id_embed"></div>
	</div>

id可以自己随意设置

将从GitHub下载的文件中的gt-php-sdk-master\lib\class.geetestlib.php 文件拷到tp相应目录,并改成.class.php后缀(方便引入),别忘了geetestlib里面极验验证id和key要进行修改成C("CAPTCHA_ID")和C("PRIVATE_KEY");

在登录验证模块中新建StartCaptchaServlet方法,代码如下

{% highlight php linenos %}
	public function StartCaptchaServlet(){
		error_reporting(0);
		$GtSdk = new \Admin\Library\Geetestlib();
		$return = $GtSdk->register();
		if ($return) {
		    $_SESSION['gtserver'] = 1;
		    $result = array(
		            'success' => 1,
		            'gt' => C('CAPTCHA_ID'),
		            'challenge' => $GtSdk->challenge
		        );
		    echo json_encode($result);
		}else{
		    $_SESSION['gtserver'] = 0;
		    $rnd1 = md5(rand(0,100));
		    $rnd2 = md5(rand(0,100));
		    $challenge = $rnd1 . substr($rnd2,0,2);
		    $result = array(
		            'success' => 0,
		            'gt' => C('CAPTCHA_ID'),
		            'challenge' => $challenge
		        );
		    $_SESSION['challenge'] = $result['challenge'];
		    echo json_encode($result);
		}
	}
{% endhighlight %}


登录界面加入如下js代码

{% highlight php linenos %}
<script type="text/javascript">		
	var gtFailbackFrontInitial = function(result) {
		var s = document.createElement('script');
		s.id = 'gt_lib';
		s.src = 'http://static.geetest.com/static/js/geetest.0.0.0.js';
		s.charset = 'UTF-8';
		s.type = 'text/javascript';
		document.getElementsByTagName('head')[0].appendChild(s);
		var loaded = false;
		s.onload = s.onreadystatechange = function() {
			if (!loaded && (!this.readyState|| this.readyState === 'loaded' || this.readyState === 'complete')) {
				loadGeetest(result);
				loaded = true;
			}
		};
	}
	//get  geetest server status, use the failback solution
	var loadGeetest = function(config) {

		//1. use geetest capthca
		window.gt_captcha_obj = new window.Geetest({
			gt : config.gt,
			challenge : config.challenge,
			product : 'popup',
			offline : !config.success
		});

		gt_captcha_obj.appendTo("#div_id_embed").bindOn('#click');
	}

	s = document.createElement('script');
	s.src = 'http://api.geetest.com/get.php?callback=gtcallback';
	$("#div_geetest_lib").append(s);
	
	var gtcallback =( function() {
		var status = 0, result, apiFail;
		return function(r) {
			status += 1;
			if (r) {
				result = r;
				setTimeout(function() {
					if (!window.Geetest) {
						apiFail = true;
						gtFailbackFrontInitial(result)
					}
				}, 1000)
			}
			else if(apiFail) {
				return
			}
			if (status == 2) {
				loadGeetest(result);
			}
		}
	})()
	$.ajax({
		url : "__CONTROLLER__/StartCaptchaServlet/rand/"+Math.round(Math.random()*100),
		type : "get",
		dataType : 'JSON',
		success : function(result) {
			console.log(result);
			gtcallback(result)
		}
	})
</script>
{% endhighlight %}

通过以上步骤就可以在页面生成一个极验验证的滑动验证码咯而且是点击登录弹出验证码的那种,后台验证如下

{% highlight php linenos %}
public function checkLogin(){
	$GtSdk = new \Admin\Library\Geetestlib();
	if($_SESSION['gtserver'] == 1){
    	$result = $GtSdk->validate($_POST['geetest_challenge'], $_POST['geetest_validate'], $_POST['geetest_seccode']);
	    if($result == TRUE){
	        echo 'Yes!';
	    }else if($result == FALSE){
	        echo 'No';
	    }else{
	        echo 'FORBIDDEN';
	    }
	}else{
	    if($GtSdk->get_answer($_POST['geetest_validate'])){
	        echo "yes";
	    }else{
	        echo "no";
	    }
	}
}
{% endhighlight %}

打完收工:P