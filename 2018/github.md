---
title: Linux终端上的github基础教程
date: 2018-07-20 13:40:09
tags: base
---

# 欢迎各位来到木犀团队后端组星计划Github部分

经过之前的学习，相信大家现在已经能够进入Linux系统了！
恭喜你们，现在有了一个比Windows好些的开发平台。
接下来我的这篇博客的目的是教你们使用开发者手中的利器--github.com

## 什么是Github

简单而非学术的说：就是大家放代码的地方。
你可以放自己的练习代码。
也可以和别人一起开发。
当然别人的代码是可以公开的，而你的代码也可以是公开的。

这是一个开源网站，是属于开发人员的圣地--github

--------

## 首先我们应该拥有一个账户！

不要以为纯英文界面都会被墙！
github是使用国内的网络就可以访问的！
网址：https://github.com
或者百度github

![这里写图片描述](https://img-blog.csdn.net/20180719232322136?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoaWluYV9PcmV6/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这里是首页，如果心细的话可以去看看首页的话都是什么意思www
我们点击右上角的Sign Up（注册

![这里写图片描述](https://img-blog.csdn.net/20180719232400451?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoaWluYV9PcmV6/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

在下面填写个人信息，注意不能使用重复的用户名和邮箱。
这里要注意用户名使用英文的，以免带来不必要的麻烦。
密码设置要有一定的复杂度！
我们点击create an account

![这里写图片描述](https://img-blog.csdn.net/20180719232547131?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoaWluYV9PcmV6/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

第二步这里不要更改，直接点击continue

![这里写图片描述](https://img-blog.csdn.net/20180719232703768?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoaWluYV9PcmV6/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

第三步，这里是让你写一些问题的答案。
第一个问题是问你写代码的水平，如果你是小白的话就选第一个咯。
第二个问题是问你为什么注册，我们选择Project Management.
第三个问题是问你什么职业，显然是第一个。

然后你就创建成功了！
![这里写图片描述](https://img-blog.csdn.net/20180719232900810?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoaWluYV9PcmV6/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这里有人发现不会验证邮箱对不对，但是千万不要大意，这个邮箱非常的重要的！！

-----

##  下载安装git-core

Ubuntu系统的终端使用快捷键：Ctrl+Alt+T开启（T代表terminal）

然后会出现一个很酷酷的命令行界面：

![这里写图片描述](https://img-blog.csdn.net/20180720110641451?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoaWluYV9PcmV6/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

你会发现命令行界面总有一串字符串：（以我自己的为例）

	shiina@shiina-Inspiron-7559:~$

在@前面是你这个Linux下的用户，默认是你进入系统的用户。但是在某些状态下，我们需要使用超级用户权限。

我们使用

	sudo su

这个命令，然后键入你的密码，就可以切换为超级管理员，此时你发现你的身份从shiina变成了root，root就是根用户，是超级用户（super user）

然后使用快捷键Ctrl+D退回，我们回到shiina这个用户的状态。

	虽然超级用户是一个很方便的东西，但是要注意不要乱用。超级用户时创建更改的所有文件都对于普通用户是只读状态。如果要修改要递归更改权限。
	谨慎使用。

然后我们使用

	sudo apt-get update
	
	sudo apt-get install git-core

sudo就是使用super user的权限去do，可以使单独的命令具有超级用户权限。

apt-get暂时可以看作是手机上的应用商店。update是更新的意思，我们先更新一下自己系统中的"应用商店"。

install是安装的意思。git-core就是我们要安装的应用名称。怎么样，是不是简单明了？

安装成功之后，输入

	git --help

来查看git这个"应用"的一些功能。

![这里写图片描述](https://img-blog.csdn.net/20180720112205988?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoaWluYV9PcmV6/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

-----

## 把你之前注册的账户绑定到本地

使用命令：

	git config --global user.name "your_name"
	git config --global user.email "your_email"

这两个命令的作用是告知git这个"应用"你的身份信息。

	ps:your_name和your_email务必要填写正确

-----

## 创建一个仓库叭w

使用浏览器之前的github官网页面，右上角进入个人主页：

![这里写图片描述](https://img-blog.csdn.net/20180720123119568?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoaWluYV9PcmV6/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后点击到仓库（repository）的选项：

![这里写图片描述](https://img-blog.csdn.net/2018072012333550?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoaWluYV9PcmV6/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

有一个很瞩目的**NEW**按钮，我们点击：
![这里写图片描述](https://img-blog.csdn.net/20180720123541545?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoaWluYV9PcmV6/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后填写相关信息，包括仓库名称，描述信息等等

![这里写图片描述](https://img-blog.csdn.net/20180720124335381?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoaWluYV9PcmV6/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后点击**Create Repository ** 

出现了！是一堆看不懂的东西！

这个页面代表你已经在github网站上创建了一个你自己的仓库。什么是仓库呢？就是集中放一类代码的地方。

我们现在有仓库了，但是仓库里空空如也，如果使用浏览器页面进行交互，不仅上传文件太慢，而且没有逼格。

-----

## 使用终端进行文件上传！

wa怎么这么长啊这个博客（抱怨）
但是不要担心，这是最后一步了！

还记得刚才的那个全是看不懂语句的页面吗？不要关掉！我们来分析这个页面。
（当然关掉也无所谓啦～

![这里写图片描述](https://img-blog.csdn.net/20180720125237761?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoaWluYV9PcmV6/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

在被蓝色高亮的部分，有一个链接在框框里，这个框框里的链接就是你的仓库的URL（统一资源定位符）
	
	https://github.com/YOUR_NAME/REPOSITORY_NAME.git

这么一种格式↑很好记的，记住的话就可以自然而然写出这个链接。

视线向下移动，有一句话叫做：

	...or create a new repository on the command line
	。。。或者用命令行创建一个新的仓库

下面的命令挨个输入到命令行就好了...但是需要一些事先准备：

仓库在电脑本机上的出现形式是：文件夹
所以我们要用命令行创建新文件夹ww
和视图界面不同。。我们不需要 鼠标

	mkdir REPOSITORY_NAME
	//REPOSITORY_NAME是你仓库的名字
	cd REPOSITORY_NAME
	//进入文件夹

然后就可以执行网页给你的命令了，但是我要更改一下顺序：

	git init
	//把这个文件夹变成本地的一个仓库
	git remote add origin https://github.com/YOUR_NAME/REPOSITORY_NAME.git
	//把本地仓库和github上的仓库对接起来

此时，我们已经把本地仓库和远程仓库对接起来了w，接下来我们新建一个文件，然后上传！

	echo "# REPOSITORY_NAME" >> README.md
	//新建一个名为README.md的文件，其内容为："REPOSITORY_NAME"
	git add README.md
	//将文件README.md 加入即将上传的空间
	git status
	//你可以看到这个文件的状态
	git commit -m "my first commit"
	//确认添加完了，我们提交即将上传的文件到缓冲区
	git push origin master
	//推上远程仓库，在这之后会让你输入用户名和密码，在这里的origin就是之前使用git remote语句链接到的远程仓库的链接地址。

到现在你应该就完成了基本任务。但是git博大精深，所以暂时的放松之后。记得看我在后面写的其余知识点。

-----

## 其他
	


