---
layout: post
title: 让php实现更快的文件下载
category: PHP
tags: php
keywords: 
description: 
---

> 好久没更新了..因为最近都挺忙的,以后会定时抽出时间更新,虽然没人看- -
> 本来一个很简单的文件下载,ie通过,谷歌死活没反应,鼓捣了一番....突然发现是谷歌迅雷扩展搞的鬼-,-禁用掉就正常了,本文是鼓捣中途的一些收获,原文地址[风雪之隅-让PHP更快的提供文件下载](http://www.laruence.com/2012/05/02/2613.html)

## 实现代码

{% highlight php linenos %}
<?php
    $file = "/tmp/中文名.tar.gz";
    $filename = basename($file);
    header("Content-type: application/octet-stream");
    //处理中文文件名
    $ua = $_SERVER["HTTP_USER_AGENT"];
    $encoded_filename = rawurlencode($filename);
    if (preg_match("/MSIE/", $ua)) {
     header('Content-Disposition: attachment; filename="' . $encoded_filename . '"');
    } else if (preg_match("/Firefox/", $ua)) {
     header("Content-Disposition: attachment; filename*=\"utf8''" . $filename . '"');
    } else {
     header('Content-Disposition: attachment; filename="' . $filename . '"');
    }
    //让Xsendfile发送文件
    header("X-Sendfile: $file");
{% endhighlight %}

原文作者考虑到下载文件有可能有中文名,为了解决这个问题首先用`$_SERVER["HTTP_USER_AGENT"]`获取用户浏览器信息,然后用正则判断是ie还是火狐还是其他,并分别做处理,最后没有用`fread()`或者`file_get_contents()`是考虑到了:


`输出的时候, 如果是Apache + PHP mod, 那么还需要发送到Apache的输出缓冲区. 最后才发送给用户. 而对于Nginx + fpm如果他们分开部署的话, 那还会带来额外的网络IO.
那么, 能不能不经过PHP这层, 直接让Webserver直接把文件发送给用户呢?
我们可以使用Apache的module mod_xsendfile, 让Apache直接发送这个文件给用户`


也就是:

{% highlight php linenos %}
    header("X-Sendfile: $file");
{% endhighlight %}

这样可以跳过php直接用apache发送文件给用户,达到更快的目的,文章写得精彩且通俗易懂,但是有点小瑕疵....因为php自带的basename()函数本身就不支持中文啊也就是说如果文件是中文名的话`$filename = basename($file);` 就直接把文件名过滤掉了,更别说接下来的操作了,小生站在巨人的肩膀上做了一些简单的改进..

{% highlight php linenos %}
<?php
    $file = "/tmp/中文名.tar.gz";
    $filename = preg_replace('/^.+[\\\\\\/]/', '', $file);
    header("Content-type: application/octet-stream");
    //处理中文文件名
    $ua = $_SERVER["HTTP_USER_AGENT"];
    $encoded_filename = rawurlencode($filename);
    if (preg_match("/MSIE/", $ua)) {
     header('Content-Disposition: attachment; filename="' . $encoded_filename . '"');
    } else if (preg_match("/Firefox/", $ua)) {
     header("Content-Disposition: attachment; filename*=\"utf8''" . $filename . '"');
    } else {
     header('Content-Disposition: attachment; filename="' . $filename . '"');
    }
    //让Xsendfile发送文件
    header("X-Sendfile: $file");
{% endhighlight %}

谷歌,ie,火狐均测试通过


打完收工 :P