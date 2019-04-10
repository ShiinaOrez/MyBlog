## 前言：

WRAPPERS，即```wrappers.py```。包含了请求基类（BaseRequest）、响应基类（BaseResponse）和诸多的混入类（Mixin Classes）。
```wrappers.py```是我选择进行源码解读的第一个入口，因为在写Web应用的过程中永远是离不开**请求**和**响应**的，那么首当其冲的就是要理解这两个至关重要又联系紧密的类了。

在```wrappers.py```中，首先映入眼帘的是开发者们留下的一段话：

**The request and response objects wrap the WSGI environment or the return value from a WSGI application so that is another WSGI application (wraps a while application).
请求和响应对象分别封装了WSGI环境变量和由一个符合WSGI标准的应用返回的数据，所以其实这是另一种形式的WSGI应用。**

分开详细的讨论一下：

+ Request Object：
	+ 接受WSGI-Environment为参数进行初始化，并且提供了**一系列的方法**用来解析和利用```environ```中的信息，方便客户代码更友好的利用这些来自底层的数据。
+ Response Object：
	+ 接受数据和一个```mimetype```作为参数进行初始化，并且实例是一个**可调用对象**（Callable），类似的，和WSGI-Application一样，接受两个参数：```environ```和```start_response```。接下来便来看一下是如何调用的。

-----
## ```werkzeug.wrappers.BaseResponse.__call__()```

```python
def __call__(self, environ, start_response):
    app_iter, status, headers = self.get_wsgi_response(environ)
    start_response(status, headers)
    return app_iter
```
-----
## Request和Response的应用场景：

接下来将通过```Request```和```Response```来写一个符合WSGI规范的小小应用，来帮助理解他们的应用场景;

```python
from werkzeug.wrappers import Request, Response

def application(environ, start_response):
    request = Request(environ)
    response = Response("Hello, %s!" % request.args.get('name'))
    return response(environ, start_response)
```
以上代码展示了很基础的，微小的应用，它的逻辑是非常清晰的：

+ 组织请求
+ 由请求构建响应
+ 返回数据

 
在现如今的应用中，基本都符合这种“请求-响应”模式。**在该模式中，构建请求的相似度极高，而对请求的处理逻辑是每个应用的特点和灵魂。**因此werkzeug使用了装饰器句法，讲处理```environ```为请求的部分抽离了出来。
[我觉得可能这是开创了使用装饰器句法装饰view_function的先河]

```python
@Request.application
def application(request):
    ...
    ...
```
现在我们就要正式的开始我们的“第一个”解析源码的过程了：

-----
## ```werkzeug.wrappers.BaseRequest.application()```

```python
@classmethod
def application(cls, f):
    from werkzeug.exceptions import HTTPException
    def application(*args):
        request = cls(args[-2])
        with request:
            try:
                resp = f(*args[:-2]+(request,))
            except HTTPException as e:
                resp = e.get_response(args[-2])
            return resp(*args[-2:])
    return update_wrapper(application, f)
```
第一眼看到这个东西我是真的不想读，真的真的不想读。

首先要解决看不懂的方法：```get_response()```、```update_wrapper()```
### ```werkzeug.exceptions.HTTPException.get_response()```
该方法的注释是这么写的：

Get a response object. If one was passed to the exception it's returned directly.
那么这个方法的目的就相当明显，就是**从错误类中获取响应对象**。
### ```functools.update_wrapper()```
该方法用于保持函数签名。著名的```functools.wraps```装饰器就是由这个方法实现的。

在解决了这两个看上去陌生的方法之后，我们继续分析application：
+ 导入了werkzeug中所有错误类的基类：```HTTPException```
+ 在内部定义的```application()```中，使用```args[-2]```来获取```environ```用于初始化```BaseRequest```
+ 以生成的request实例为上下文管理器：
	+ 获取一个响应对象并返回，即实现原本被装饰函数的功能
	+ 尝试将原参数元组最后的两个参数替换为request对象
	+ 否则，则从错误类获取响应


有趣之处在于使用的```with```句法：显然就需要去看一下```__enter__()```和```__exit__()```这两个魔法方法（Magic Method）
但是我们找了以下注释：

Closes associated resources of this request object.  This closes **all file handles** explicitly. **You can also use request object in a with statement which will automatically close it.**
由此我们可以得知，请求时不只是有单纯的数据流动，也有可能涉及到文件的打开什么的。这个时候使用```with```，最后在```__exit__()```中同意关闭文件是最理想的选择。

-----

接下来就是要正式接触对于源码的解析了w
真的好让人**兴♂奋**啊~！

