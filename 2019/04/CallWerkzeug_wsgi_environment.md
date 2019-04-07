## WSGI-Environment是什么：

WSGI-Environment，就是在代码中最常见的**environ**。
environ**字典**被用来包含**CGI**(Common Gateway Interface，必须的网关接口)环境变量，除非是空字符串，否则所有以下将要介绍的变量都**必须**指定，但是在空字符串的情况下，他们会被忽略。除非特殊说明。
简而言之：**environ是一个字典，包含请求中所有的环境变量**

-----
## environ经典情况下包含的键值对：

| Key | Value | Required | Meaning | Example |
|------|-------|---------|-------------|----------|
| REQUEST_METHOD | string | √ | HTTP的请求方式 | "GET" |
| SCRIPT_NAME | string |  | URL请求中path的开始部分，对应了应用程序对象 | "/api/v1"|
| PATH_INFO | string |  | URL请求中path的其余部分，指定应用程序内部的虚拟位置 | "/login" |
| QUERY_STRING | string |  | 请求参数 | "id=10086&page=1" | 
| CONTENT_TYPE | string |  | HTTP请求中的Content-Type字段 | "application/json" |
| CONTENT-LENGTH | string |  | HTTP请求中的Content-Length字段 | "18868" |
| SERVER_NAME | string | √ | 服务器名 | "119.27.79.87" |
| SERVER_PORT | string | √ | 服务器端口 | "6666" | 
| SERVER_PROTOCOL | string | √ | HTTP协议版本 | "HTTP/1.0" |
| HTTP_* |  |  | 客户端提供的请求报头们，以"HTTP_"开头|  |

-----
## WSGI要求下的变量：

| Name | Type | Meaning |
| ------ | ---------| ------ |
| wsgi.version | tuple | 一般为(1,0)，代表WSGI版本1.0 | 
| wsgi.url_scheme | string | URL的scheme部分，值为"http"或"https" |
| wsgi.input | stream | 一个能被HTTP请求主体读取的输入流 |
| wgsi.errors | stream | 用于记录错误信息的输出流，目的是记录程序或者其他标准化及可能的中心化错误。对很对服务器而言，wsgi.errors是服务器的主要错误日志 |
| wsgi.multithread | bool | 如果一个应用程序对象同时处于被同一个进程中的不同线程调用，则为True | 
| wsgi.multiprocess | bool | 如果相同的应用程序对象同时被其他进程调用，则为True |
| wsgi.run_once | bool | 如果服务器/网关期待（但是不保证）应用程序在它所在的进程生命期间只会被调用一次，则为True |

-----
## WSGI-Environment的规范：

+ 一个遵循WSGI规范的服务器或网关应该在文档中描述它们自己定义的同时，适当的**说明**一下它们可以提供哪些变量。而应用程序这边则应该对它们要用到的**每一个变量的存在性进行检查**，并且在检测某些变量不存在的时候要有**备用措施**。
+ 除了**CGI定义的变量**，environ字典也可以包含任何**操作系统相关的环境变量**，并且并且必须包含**第二部分提及的变量**。
+ environ字典可能会包含**服务器定义的**变量。这些变量应该用**小写，数字，点号**以及**下划线**来命名，并且必须定义一个该服务器/网关**专有的前缀开头**。

-----
