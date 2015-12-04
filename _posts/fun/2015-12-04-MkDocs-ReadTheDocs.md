---
layout: post
title: MkDocs + Read The Docs
category: fun
tags: mk
keywords: 
description: 
---

## 概述

学习CI的过程中,发现CI的文档是用Read The Docs写的,于是研究了下,这货可以选择sphinx和mkdocs,个人对markdown比较熟悉,于是选择mkdocs,而安装mkdocs需要先安装python和pip -- [windows下面安装Python和pip终极教程](http://www.tuicool.com/articles/eiM3Er3)

---

## 安装

安装完成后可以通过以下命令查看是否安装了上述依赖:

    $ python --version
    Python 3.4.3
    $ pip --version
    pip 7.1.2

MkDocs 支持 Python 2.6, 2.7, 3.3 和 3.4.

使用 pip 安装 `mkdocs` :

    $ pip install mkdocs

`mkdocs`已经安装到你的系统. 运行 `mkdocs -h` 以检查是否正确安装.

    $ mkdocs -h
    mkdocs [help|new|build|serve|gh-deploy] {options}

---

## 开始

输入以下命令以开始一个新项目.

    $ mkdocs new my-project
    $ cd my-project

我们看一下已经创建的初始化项目.为方便,先在GItHub Create a new repository,然后clone到本地,再把new的mkdocs project复制到repository中.再来看文件结构.

有一个配置文件 `mkdocs.yml`, 和一个包含文档源码的 `docs` 文件夹. 在 `docs` 文件夹里包含了一个名为 `index.md` 的文档.

MkDocs 包含了一个内建的服务器以预览当前文档. 控制台切换当前目录到 `mkdocs.yml` 配置文件相同文件夹, 输入 `mkdocs serve` 命令以启动内建服务器:

    $ mkdocs serve
	Running at: http://127.0.0.1:8000/

在浏览器中打开 [http://127.0.0.1:8000/](http://127.0.0.1:8000/).你会看到一个基础的mkdoc.


内建服务器支持在配置文件,文档目录或主题发生改变时自动载入并重新生成文档.

编辑 `docs/index.md` 文件并保存. 刷新浏览器你将看到文档被同步更新.

现在可以开始编辑配置文件 `mkdocs.yml` 了.  把 `site_name` 改成其他内容并保存文档.刷新浏览器你将看到网页标题已发生改变.


## 添加页面


现在为文档添加第二个页面:直接在DOC目录下创建`about.md`

要为文档添加导航条, 只需在配置文件中添加导航条需要的标题和排序即可:

    site_name: MkLorum
    pages:
    - [index.md, Home]
    - [about.md, About]

刷新浏览器即可看到 `Home` 和 `About` 导航栏目.

## 配置主题

可以在配置文件中修改文档主题. 在 `mkdocs.yml` 中添加如下内容:

    site_name: MkLorum
    pages:
    - [index.md, Home]
    - [about.md, About]
    theme: readthedocs

刷新浏览器即可看到 ReadTheDocs 主题已被应用.


## 站点生成

我们现在已经可以发布 `MkLorum` 文档了. 通过以下命令生成文档.

    $ mkdocs build

该命令创建了一个 `site` 新目录. 

注意源码被分别输出为 `index.html` 和 `about/index.html`.  主题中的其他文件也被复制到了 `site` 目录中.

如果你使用 `git` 等版本控制系统, 你可能不希望提交构建之后的文档到版本库.  在 `.gitignore` 中添加 `site/` 即可忽略该目录.

    $ echo "site/" >> .gitignore

如果你使用其他版本控制系统则需要查阅相关文档以确定如何忽略指定目录.

一段时间后, 可能有文件被从源码中移除了, 但是相关的文档仍残留在 `site` 目录中. 在构建命令中添加 `--clean` 参数即可移除这些文档.

    $ mkdocs build --clean

## 发布

文档编写完毕后把仓库中的代码提交到github,然后到[ReadTheDocs官网](https://readthedocs.org/)注册用户.然后import a project

	1.直接把代码仓库地址输.

	2.可以选择connect github,然后选择仓库

以上两种方法都可行.

最后构建下,管理(设置)下,你就会发现文档已经生成了

打完收工:P