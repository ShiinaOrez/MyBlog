## 源码中对应的场景：
在```werkzeug.wrappers.BaseRequest.delete_cookie()```和```werkzeug.wrappers.BaseRequest.set_cookie()```方法的参数中出现了path和domain。
在看到的时候不是很理解，只是大概知道是中文路径和域的意思，在查阅了相关资料之后做一个总结。

-----
## path：
	path代表cookie所在的目录，默认为'/'，也就是根目录。
### 访问限制：
在同一个服务器上有目录如下：
**/test/**、**/test/cd/**、**/test/dd/**
则```path = /test/```的cookie对于全部目录下都可以访问。
　```path = /test/cd/```的cookie不能被```/test/dd/```下所访问。

-----
## domain：
	domain为cookie所在的域，默认为请求的地址。
若访问```www.test.com/test/test.jpg```，则domain默认为```www.test.com```
### 跨域访问：
设域A：```t1.test.com```
   域B：```t2.test.com```
则域A域B都能访问的cookie域应该设为```.test.com```
域A不能访问，域B能访问：设为```t2.test.com```

-----
