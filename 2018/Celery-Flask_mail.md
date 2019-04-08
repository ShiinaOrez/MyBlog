---
title: Clery-Flask_mail
date: 2018-03-30 12:12:12
tags: python
---

##  前言(・ω<) ﾃﾍﾍﾟﾛ

> * 啊学校的抢课进不去。。好想准时抢课(╥╯^╰╥)
> * 啊朋友生日要到了，可是我不想熬夜啊啊(╥╯^╰╥)
> * 啊拖延症。。真想给自己发封邮件提醒一下(╥╯^╰╥)

看完这篇博客，ok的ヾ(ﾟ∀ﾟゞ)

-----

## 先行知识：什么是celery？什么是flask？

### **Celery~[芹菜]~:**

Celery 是一个由 Python 编写的简单、灵活、可靠的用来处理大量信息的分布式系统,它同时提供操作和维护分布式系统所需的工具。

Celery 专注于实时任务处理，支持任务调度。

说白了，它是一个分布式队列的管理工具，我们可以用 Celery 提供的接口快速实现并管理一个分布式的任务队列。

### **Flask~[瓶子]~：**

Flask是一个使用 Python 编写的轻量级 Web 应用框架。其 WSGI 工具箱采用 Werkzeug ，模板引擎则使用 Jinja2 。Flask使用 BSD 授权。

	Flask_mail是flask的一个重要的插件。Flask_mail 连接到简单邮件传输协议 (Simple Mail Transfer Protocol, SMTP) 服务器，并把邮件交给这个服务器发送。
	
	本文章着重于讲解celery，因此对于flask部分不再赘述。
	
	我假设读者会用flask_mail发送邮件。。如果你不会，那么请参考以下文章：
	
	http://pythonhosted.org/Flask-Mail/
	https://blog.csdn.net/wbin233/article/details/73222027

-----

## 给我个例子吧，到底什么是celery

celery的原理很简单，就是一个异步任务队列。
所以开始我们的第一个小实验。

-----

### 第一个celery程序：

> * 依赖：python、redis-server、celery 没有的直接sudo pip3 install XXX

```python
# test.py

from celery import Celery
import time 
 
app=Celery('tasks',backend='redis://localhost:6379/0',broker='redis://localhost:6379/0') #配置好celery的backend和broker
 
@app.task  #普通函数装饰为 celery task
def add(x,y):
    time.sleep(5.0) #模拟耗时操作
    return x+y
```

(〝▼皿▼)**wa你这个sb博主为什么先放代码？**
别急别急，慢慢解释。

celery的异步任务队列是怎么实现的呢？一张图很好的解释了。↓

![celery图解](https://ooo.0o0.ooo/2016/12/10/584bbf78e1783.png)

#### 什么是Broker？：
	brokers 中文意思为中间人，在这里就是指任务队列本身，Celery 扮演生产者和消费者的角色，brokers 就是生产者和消费者存放/拿取产品的地方(队列)
	常见的 brokers 有 rabbitmq、redis、Zookeeper 等

	我们今天使用的便是redis

#### 什么是Backend？：

	顾名思义就是结果储存的地方，队列中的任务运行完后的结果或者状态需要被任务发送者知道，那么就需要一个地方储存这些结果，就是 Result Stores 了

	常见的 backend 有 redis、Memcached 甚至常用的数据库都可以。
#### 什么是Worker？：
	就是 Celery 中的工作者，类似与生产/消费模型中的消费者，其从队列中取出任务并执行

弄明白这些定义之后，我们就开始一行行地解释代码：

> * 首先，是预处理，我们引入了Celery用于创造Celery实例，引入了time包
> * 其次创造一个Celery实例，名为app
	> 三个参数：第一个是name,第二个是broker，第三个是backend
	//这里的broker和backend是以redis的形式给出的，localhost==127.0.0.1

> * 写一个简单的函数add，并且用app.task装饰
	> 使之成为能加入broker的task。。。

这基本就解释完了，那么问题来了

ヽ(￣▽￣)ﾉ我知道了，接下来就是python3 test.py对不对
(｀ﾟДﾟ´)ゞ你知道个毛线啊！才不是！
(|| ﾟДﾟ)你redis启动了没有啊！
Σ(っ°Д°;)っ你celery也没开啊！

```
$ redis-cli -p 6379 #开放6379端口
新终端
$ celery -A test worker --loglevel=info 或者 debug
```

当然你可以加个&后台运行。。但是用ps关掉的时候就比较麻烦了。。
这个时候，用脚本来把任务加入队列也好，用shell也好，都ok

```
>>> from test import add
>>> add.aplly_async(args=(2,8))
>>>> ...
```
-----

### 怎么系统的写一个celery任务呢？

٩(๑>◡<๑)۶ 首先你要会写一个flask项目的结构。。。

ヽ(ﾟ∀ﾟ)ﾒ(ﾟ∀ﾟ)ﾉ 听说结构是这个样子的↓：

```
celery_test //测试总目录
    |------celery_app //存放celery相关代码
    |          |------__init__.py  //构造器
    |          |------celery_config.py //celery配置文件
    |          |------task.py //任务文件，比如什么发邮件啊，什么抢课啊
    |------run.py //应用程序，用于将你的task加入celery broker
```

```python
# celery_test/celery_app/__init__.py

from celery import Celery

app=Celery('demo') //实例化
app.config_from_object('celery_app.celery_config') //导入配置

```

```python
# celery_test/celery_app/celery_config.py

from datetime import timedelta
from celery.schedules import crontab

# Broker and Backend
BROKER_URL = 'redis://localhost:6379/1'
CELERY_RESULT_BACKEND = 'redis://localhost:6379/0'

# Timezone
CELERY_TIMEZONE='Asia/Shanghai'    # 指定时区，不指定默认为 'UTC'
# CELERY_TIMEZONE='UTC'

# import
CELERY_IMPORTS = (
    'celery_app.task'
)

# schedules
CELERYBEAT_SCHEDULE = {
    'add-every-30-seconds': {
         'task': 'celery_app.task.send_mail',
         'schedule': timedelta(seconds=30),      # 每 30 秒执行一次
#         'args': ()                             # 任务函数参数
    },
    'multiply-at-some-time': {
        'task': 'celery_app.task.lession',
        'schedule': crontab(hour=9, minute=50),  # 每天早上 9 点 50 分执行一次
#       'args': ()                               # 任务函数参数
    }
}
```

```python
# celery_test/celery_app/task.py 

from flask import Flask ...
from celery_app import app
import ...

...

@app.task
def send_mail():
    ...

@qpp.task
def lession():
    ...

if __name__=='__main__':
    flask.run()
```

```python
# celery_test/run.py

from celery_app import task

task.send_mail.aplly_async()
task.lession.apply_async()

```
ヾ(ｏ･ω･)ﾉ 介要三分钟，李就会杠我一样，爱上这款celery

哦对。。(>ω･* )ﾉ还要解释一下celerybeat

#### **celerybeat：**
celery中的定时任务是由celerybeat发送到broker的。。
因此我们不仅要启动redis，celery worker还有celerybeat

比如：
	
	$ celery -A celery_app worker --loglevel=info

-----
d(´ω｀*)Shiina的celery讲解就这么结束了w
不知道有没看看懂呢各位wヾ(=･ω･=)o
!!!∑(ﾟДﾟノ)ノ什么？flask_mail不会写？别找我啊！
 щ(ﾟДﾟщ) 什么？request不会写？别找我啊！
  (;ﾟДﾟi|!) 什么？你要看我的代码？
  
(灬°ω°灬) 拿去：https://github.com/ShiinaOrez/celery_test/ 别忘了顺手follow我哦！

-----
顺便贴上我GitHub博客的地址：
https://shiinaorez.github.io

ヽ(´•ω•`)､那么说再见了w
(>ω･* )ﾉ希望你们能爱上我w

![这里写图片描述](http://img.blog.csdn.net/20180329235544148?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU2hpaW5hX09yZXo=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
