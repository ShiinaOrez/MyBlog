---
title: QT安装以及配置教程
date: 2018-04-11 23:55:12
tags: ubuntu QT
---

# 什么是QT

	Qt 是一个1991年由Qt Company开发的跨平台C++图形用户界面应用程序开发框架。它既可以开发GUI程序，也可用于开发非GUI程序，比如控制台工具和服务器。Qt是面向对象的框架，使用特殊的代码生成扩展（称为元对象编译器(Meta Object Compiler, moc)）以及一些宏，Qt很容易扩展，并且允许真正地组件编程。 -- 百度百科

QT官网： https://www.qt.io/

# 大体流程？

> * 下载QT opensource安装包
> * 安装JDK
> * 新建项目或者打开项目
> * 配置项目
> OJBK

## Install QT Creator

### ① 在QT官网下载：

https://www.qt.io/download
选择开源版本，新用户填写信息注册，老用户直接登陆即可。然后会下载一个名为：
	qt-unified-linux-x64-3.0.4-online .run
的文件。双击运行或者命令行./运行都可以。

然后一直next,不要停...

安装完毕。

### ②$apt-get install qtcreator

首先我们更新apt源：

	$ vim /etc/apt/sources.list

在list文件末尾追加：
	
	deb http://us.archive.ubuntu.com/ubuntu trusty main universe

然后就可以放飞自我了：

	$ sudo apt-get install qtcreator

## 安装JDK

由于QT运行是需要JDK支持的，所以我们要安装JDK(version>=8)

## 打开项目

现在你可能看到的是这个界面（我假设你安装好了）

![这里写图片描述](http://img.blog.csdn.net/20180411234219716?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU2hpaW5hX09yZXo=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

然后Ctrl+O就可以打开项目了，打开pro文件

## 配置项目

我假设你真的打开了一个项目

![这里写图片描述](http://img.blog.csdn.net/20180411234703400?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU2hpaW5hX09yZXo=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

你可能发现你的界面和我的不太一样woc，没关系，找到manage kits选项【就在左上角】

然后选择你的默认kit

![这里写图片描述](http://img.blog.csdn.net/20180411235117234?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU2hpaW5hX09yZXo=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

然后选择编译器，还有QT Version选项，全都选择完之后点击OK，你会发现你配置完了。

编译，运行！开始你的GUI编程吧！

------

任何问题欢迎电子邮件：

shiina_orez@qq.com or shiinaorez@gmail.com

-----
