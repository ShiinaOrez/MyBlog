---
title: login_required身份验证用装饰器for api [flask version]
date: 2018-08-18 17:50:32
tags: python
---

## 涂api是一件莫名其妙就会有绊脚石的工作

之前涂api，每一个api都要传token（身份验证令牌），然后调用confirm方法比较等等。
这次涂api总得进步不是。。于是就要把身份验证抽离。
按照常规的做法，这次我要写一个**decorator**，叫做**login_required**

对了这次博客讲解的[代码](https://github.com/Muxi-X/work.muxixyz_be_auth/blob/shiina/work_muxixyz_app/decorator.py)

----
### 准备工作：
嘤嘤嘤...本来想抄抄[可可](https://github.com/Humbertzhang)的代码的...后来发现是用的aiohttp这个Python框架，虽然不会差很多，但是我看着就不想抄了..

于是 ~~自己动手丰衣足食~~ google了一下,找了半天知道这玩意儿还有专业名字：

**login_required**  ~~我真的很想叫它别的名字，好听一点的~~

然后学啊学....跟着人家的经验走，最后居然花了一下午，弄完了

哦对准备工作：

+ 电脑
+ 脑子
+ 手

-----
## ~~@~~  ~~Decorator~~  装饰器

在Python里有乱七八糟的这个器那个器

朋友写了个博客，叫做[Python之器](https://darren2017.github.io/2018/04/10/python-%E4%B9%8B%E5%99%A8/#more) ~~虽然我也看不懂，但是可以看看给他增加以下浏览量~~

总之就是作用于函数，给函数加一层包装  ~~俄罗斯套娃笑死~~

-----
## ~~from..import..as..~~ 各种包

这次写login_required()用到的包

+ functools
	+ wraps  ：用来写装饰器的装饰器
+ flask
	+ abort,request ：抛出错误处理以及请求包
+ itsdangerous
	+ TimedJSONWebSignatureSerializer ：加密解密令牌

这个只是这个文件中用到的.... ~~莫名在别人博客被jwt圈粉，以后不想用Serializer了~~

-----
## 开始写一个 ~~浪费生命的~~ 装饰器


我们开始吧：
```python
def login_required(f):
```
~~函数的~~ 第一行，你以后要用的装饰器的名字嘤嘤嘤，中间的参数**f**表**function**，是即将被我们进行 ~~俄罗斯套娃式处理~~ 修饰的函数。

```python
    @wraps(f)
    def decorated_function(*args,**kwargs):
```
接下来是二三行，首先我们用到了wraps，使用了@符号代表对接下来的函数**decorated_function**使用装饰器进行装饰，所以下面的函数名字是decorate的被动 ~~本人英文稀烂~~
**值得注意的是，装饰器使用语法后不加冒号** ~~我知道你们不会注意的，但是我写错过quq~~

wraps的作用是帮助你写的函数成为装饰器

***args,** ****kwargs **代表各种各样的参数

接下来：
```python
            if not 'token' in request.headers:
                abort(401)
            t=request.headers['token'].encode('utf-8')
            s=Serializer(current_app.config['SECRET_KEY'])
            try:
                data=s.loads(t)
            except:
                abort(401)
            uid=data.get('confirm')
            return f(uid,*args,**kwargs)
```
非常抱歉我保持了缩进 ~~强迫症真的别管我~~

这里就是正文了，首先我们判断我们需要的**token**是否存在于**request.headers**中，如果不存在，说明接下来就没有必要操作了，直接**abort(401)**

	abort(status_code)用于抛出错误，而401在状态码的意义中往往表示身份验证失败

t是从headers['token']字段中encode出来的，所以不需要再次encode ~~每次encode和decode多少次我都是人肉debug~~
s是我们解密用的秘钥，在这里我们要使用包来对config中的SECRET_KEY进行处理，为解密token做准备

然后是一个解密工作，为**s.loads()**如果解密失败，也直接抛出401给前端 ~~我测试的时候瞎写状态码写成了405hhhhh~~

解密完之后，~~flask套路生成的~~ token变为了一个json数据段，我们取出名为**confirm**的数据，就是token携带的userID

最后值得注意的是，我们接受了一个函数作为参数，所以我们也要返回一个函数。这里函数别的都没有变，唯独多出个参数，目的是从装饰器的 “预操作”中取出我们**真正需要**的值。而预处理的内容，都写进了被wraps装饰的这个函数里。

最后：
```python
    return decorated_function
```

我们在login_required(f)中传进来的参数，变成了被装饰后的函数**decorated_function**，我们直接返回就好了。

然后我们就

-----
## 写完了 ~~令人头秃的~~ 装饰器

其实写好装饰器不是困难的全部，你要去学会怎么使用你自己写的装饰器，要改动你的api文件，还要写好单元测试去测试你的装饰器。这几件事情联系在一起就 ~~令人头秃~~ 比较麻烦。

-----

**我真的垃圾**

最后欢迎 ~~各位观众姥爷~~ 点个[关注](https://blog.csdn.net/Shiina_Orez)啥的
还有我的[github地址](https://github.com/ShiinaOrez)
当然还有 ~~假装是自己搭建的~~ [小小博客](shiinaorez.github.io)

~~祝各位暴打产品经理~~
 ~~祝各位福如东海，寿比南....~~
 ~~祝各位考的全会蒙的全.......~~
 ~~祝各位......~~

DEBUG不用玄学小黄鸭！
-----

------
