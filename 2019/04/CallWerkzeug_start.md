-----
## 前言：Werkzeug

[werkzeug](https://github.com/pallets/werkzeug)是使用Python语言编写的一个web后端工具包，提供了相当多的功能，并且是基于[PEP 333:WSGI](https://www.python.org/dev/peps/pep-0333/)标准的，因此易于扩展。现如今许多的服务器端框架都是使用werkzeug的，比如[flask](https://github.com/pallets/flask)。

-----
## 标准：WSGI

WSGI是**web server gateway interface**的意思，即web服务器网关接口。是在PEP 333中提出用于规范服务端接口协议的。

	PEP（Python增强建议书）： 是Python Enhancement Proposals的缩写。一个PEP是一份为Python社区提供各种增强功能的技术规格，也是提交新特性，以便让社区指出问题，精确化技术文档的提案。

**WSGI指出以下几点：**

+ WSGI接口分为两端：
	+ 服务器/网关端
	+ 应用程序/Web框架端
+ 应用程序端给服务器端提供一个可调用者（**Callable**），而服务器端如何使用它则取决于其本身。
+ 应用程序/Web框架端：
	+ 应用程序对象：一个接受两个参数的**Callable Object**
		+ 参数（environ，start_response）：
			+ environ：字典对象。字面意思是请求环境的意思，包含了一个请求应该包含的所有信息，被封装为符合标准的WSGI Environment对象。
			+ start_response：一个**Callable**，返回值是一个**Callable**或者一个能包含零个或多个字符串的可迭代对象（**Iterable**）。
				+ 参数（status，response_headers，*exc_info）：
					+ status：一个由状态码和描述拼接成的字符串。形如：```"200 OK"```
					+ response_headers：响应头。是一个列表元组。
					+ exc_info：可选参数，用于描述错误信息。
+ 服务器/网关端：每当HTTP客户端向应用程序发来一个请求，都会调用应用程序返回的**Callable**。

经过以上的描述，相信能对WSGI有一个基本的认识了，其实WSGI只是一套规范而已。但是这个规范其实远远不止这些，包括environ在内的几乎所有内容都有严格的定义。因此在后面的解释中**经常以WSGI为前缀来表示“符合WSGI标准的”**的意思。

	在PEP 333中详尽地描述了WSGI，有兴趣可以阅读@miaomiaomiao翻译后的PEP 333文档。

-----
## 源码：文件结构

作为一个工具包，werkzeug有着很严谨的文件结构。

+ ```__init__.py```
+ ```_compat.py```：维护兼容性
+ ```_internal.py```：**提供了werkzeug中使用的一些常量和基类。**
+ ```_reload.py```：重新加载模块
+ ```datastructures.py```：**数据结构模块，用来存放整合数据的类。**
+ ```exceptions.py```：**错误处理模块。用来存放各种错误类。**
+ ```filesystem.py```：文件系统模块。
+ ```formparser.py```：**表单解析模块。提供用于解析表单数据的工具函数。**
+ ```http.py```：**用于处理HTTP中的数据的工具们。在wrapper.py中使用的比较多，但是这些工具函数往往单独使用作用更加显著，尤其是在没有请求和响应对象的前提下。**
+ ```local.py```：本地上下文对象。
+ ```posixemulation.py```：为应用程序提供可移植作业系统接口（Portable Operating System Interface，POSIX）。
+ ```routing.py```：**路由匹配处理模块。**
+ ```security.py```：安全模块。
+ ```serving.py```：服务运行模块。
+ ```test.py&testapp.py```
+ ```url.py```：**处理解析URL的工具们。**
+ ```useragents.py```：用于处理用户代理的工具们。
+ ```utils.py```：**用于实现封装好的请求类和响应类的模块。对于中间件开发十分重要。**
+ ```wrappers.py```：**封装好的各种类。包括著名的请求基类和响应基类和各种混入类。**
+ ```wsgi.py```：WSGI相关模块。

加粗的是我认为比较重要且核心的模块。会在以后依次深入解读。
