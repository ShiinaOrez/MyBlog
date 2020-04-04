## 使用Nginx的一些经验

------

### nginx的安装和基本命令

安装:

```bash
$: sudo apt-get install nginx
```

启动:

```bash
$: nginx
```

停止:

```bash
$: nginx -s stop
```

重新加载配置文件:

```bash
$: nginx -s reload
```

------

### nginx配置二级域名

首先买一个域名, 然后备案, 然后去阿里云DNS解析一下.

比如我们的域名是``example.com``, 我们想要一个博客的二级域名: ``blog.example.com``, 然后我们就去DNS解析一个记录值为``blog``到我们的服务器.

然后, 配置nginx.

Linux Ubuntu的nginx的目录位于: ``/etc/nginx/``, 修改配置文件的方式有两种:

+ 直接修改/etc/nginx/nginx.conf
+ 在/etc/nginx/conf.d/目录下建立单独的配置文件

这里建议分小的配置文件进行配置, 比较的清晰, 而且效果都是一样的, 因为在``nginx.conf``里有这么一句:

```nginx
include /etc/nginx/conf.d/*.conf;
```

也就是说直接把我们的小的配置文件都包括了进去.

所以我们在``/etc/nginx/conf.d/``下新建我们的``blog.conf``, 内容如下:

```nginx
server {  
    listen 80;
    server_name blog.example.com;

    root  /etc/nginx/blog/;
    index index.html;
    }
}
```

其中``/etc/nginx/blog``是用于存放博客文件的根目录, 而``index``指定的则是首页打开的文件.

当然我们也可以通过运行在某一个端口上的服务来进行配置, 假设这个服务在``6666``端口上.

```nginx
server {  
    listen 80;
    server_name blog.example.com;

    location / {
        proxy_set_header   X-Real-IP $remote_addr;
        proxy_set_header   Host      $http_host;
        proxy_pass         http://0.0.0.0:6666;
    }
}
```

这个配置文件将``blog.example.com``的``/``目录代理到本机的``6666``端口的服务上.

--------

### 解决跨域问题

在服务端的配置文件如下进行修改:

```nginx
server {  
    listen 80;
    server_name service.example.com;

    location / {
        proxy_set_header   X-Real-IP $remote_addr;
        proxy_set_header   Host      $http_host;
        proxy_pass         http://0.0.0.0:6666;

        if ($request_method = 'OPTIONS') {
            add_header 'Access-Control-Allow-Origin' '*';
            add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
        #
        # Custom headers and headers various browsers *should* be OK with but aren't
        #
            add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range';
        #
        # Tell client that this pre-flight info is valid for 20 days
        #
            add_header 'Access-Control-Max-Age' 1728000;
            add_header 'Content-Type' 'text/plain; charset=utf-8';
            add_header 'Content-Length' 0;
            return 204;
        }
        if ($request_method = 'POST') {
            add_header 'Access-Control-Allow-Origin' '*';
            add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
            add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range';
            add_header 'Access-Control-Expose-Headers' 'Content-Length,Content-Range';
        }
        if ($request_method = 'GET') {
            add_header 'Access-Control-Allow-Origin' '*';
            add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
            add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range';
            add_header 'Access-Control-Expose-Headers' 'Content-Length,Content-Range';
        }
    }
}
```

解决跨域问题的关键在于在响应头中加入``Access-Control-Allow-*``的头部信息.

--------

### 使用gzip对传输内容进行压缩

在部署前端之后, 我发现比如使用vue构建的项目, 在加载的时候都要传输一个很大的js文件, 比如``app.js``这种, 而在传输的时候这个文件可能有10M之多, 这让我学生机的小水管直接就撑不住了, 一个网站要加载十几秒才能显示出来, 这实在是太...

所以这个时候要做的事情就有两个:

+ 一是前端尽可能对于组件进行按需引用, 尽可能的压缩这个app.js的大小
+ 二是nginx开启gzip, 并且对于application/javascript 和 text/javascript 进行压缩

在``/etc/nginx/nginx.conf``中, 默认注释掉了gzip的选项, 我们需要去掉注释, 比如这样:

```nginx
	##
	# Gzip Settings
	##

	gzip on;
	# gzip_disable "msie6";

	# gzip_vary on;
	gzip_proxied any;
	gzip_comp_level 6;
	gzip_buffers 4 8k;
	gzip_http_version 1.1;
	gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
```