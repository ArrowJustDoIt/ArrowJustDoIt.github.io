---
layout: post
title: PHP格式化时间戳(实现X小时前)
category: PHP
tags: unixtime
keywords: 
description: 
---
> 最近项目需求,其实是我自己觉得这样用户体验度比较高-,-,把之前单纯显示时间转化为显示几小时几天前

## 思路

大体思路如下：

如果是跨年并且大于3天就显示为具体的时间

如果是今天的

如果是一分钟内则显示几秒之前

如果是一小时内则显示几分钟前

如果是当天且大于一小时则显示为几小时前

如果是昨天则显示为昨天几点

如果是前天则显示为前天几点

如果大于三天(没有跨年)则显示为几月几号

根据以上思路就不难写出实现代码了：

## 简单代码

{% highlight php linenos %}
$time = $passtime;
$now = time();
$sec = $now - $time;
if($now - $time < 60){
	$passtime = $sec . '秒前';
}elseif($now - $time < 3600){
	$passtime = floor($sec / 60) . '分钟前';
}elseif($now - $time < 3600 * 60){
	$passtime = floor($sec / 3600) . '小时前';
}
{% endhighlight %}