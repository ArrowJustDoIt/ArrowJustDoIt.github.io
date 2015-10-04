---
layout: post
title: URI、URL和URN三者之间的区别
category: 技术
tags: 前端
keywords: 
description: 
---

这是一个经典的技术争论，许多人都会自问：URL、URI，很可能还有URN，它们之间的区别是什么。虽然，现在我们简单地把 URN 和 URL 都看做 URI，但严格来说URI可以进一步划分为URL、URN或者这两者的组合，所以了解这三者之间的区别将会非常有趣并让人受益匪浅。如果你恰好在某个地方碰到了这些东西，那么至少应该知道它们的含义。
***
###**definition**
这三个缩略词是Tim Berners-Lee在一篇名为RFC 3986: Uniform Resource Identifier (URI): Generic Syntax的文档中定义的互联网标准追踪协议。

- **URI**，是`uniform resource identifier`，统一资源标识符，用来唯一的标识一个资源。
- **URL**,是`uniform resource locator`，统一资源定位器，它是一种具体的URI，即URL可以用来标识一个资源，而且还指明了如何locate这个资源。
- **URN**,是`uniform resource name`，统一资源命名，是通过名字来标识资源。

##区别
首先我们要弄清楚一件事：URL和URN都是URI的子集。

换言之，URL和URN都是URI，但是URI不一定是URL或者URN。为了更好的理解这个概念，看下面这张图片。

![1](/public/img/technology/1.png)

通过下面的例子（源自 Wikipedia），我们可以很好地理解URN 和 URL之间的区别。如果是一个人，我们会想到他的姓名和住址。

URL类似于住址，它告诉你一种寻找目标的方式（在这个例子中，是通过街道地址找到一个人）。要知道，上述定义同时也是一个URI。

相对地，我们可以把一个人的名字看作是URN；因此可以用URN来唯一标识一个实体。由于可能存在同名（姓氏也相同）的情况，所以更准确地说，人名这个例子并不是十分恰当。更为恰当的是书籍的ISBN码和产品在系统内的序列号，尽管没有告诉你用什么方式或者到什么地方去找到目标，但是你有足够的信息来检索到它。

##一个用于理解这三者的例子

我们来看一下上述概念如何应用于与我们息息相关的互联网。

再次引用Wikipedia ，这些引文给出的解释，比上面人员地址的例子更为专业：

关于URL：

> URL是URI的一种，不仅标识了Web 资源，还指定了操作或者获取方式，同时指出了主要访问机制和网络位置。

关于URN：

> URN是URI的一种，用特定命名空间的名字标识资源。使用URN可以在不知道其网络位置及访问方式的情况下讨论资源。

现在，如果到Web上去看一下，你会找出很多例子，这比其他东西更容易让人困惑。我只展示一个例子，非常简单清楚地告诉你在互联网中URI 、URL和URN之间的不同。

我们一起来看下面这个虚构的例子。这是一个URI：

	http://test.com/posts/hello.html#intro

我们开始分析

	http://

是`定义如何访问资源的方式`。另外

	test.com/posts/hello.html

是`资源存放的位置`，那么，在这个例子中，

```
#intro
```
是`资源`。

URL是URI的一个子集，告诉我们`访问网络位置的方式`。在我们的例子中，URL应该如下所示：

	http://test.com/posts/hello.html

URN是URI的子集，包括`名字`（给定的命名空间内），但是`不包括访问方式`，如下所示：

	test.com/posts/hello.html#intro

就是这样。现在你应该能够辨别出URL和URN之间的不同。

如果你忘记了这篇文章的内容，至少要记住一件事：`URI可以被分为URL、URN或两者的组合`。如果你一直使用URI这个术语，就不会有错。
