---
title: nginx配置总结
date: 2017-04-18 15:54:12
categories: nginx
tags: nginx
---

# nginx的全局变量

nginx内置了大量的$开头的全局变量，这些变量在有些时候会非常有用，而且网上很少有介绍的很全面的文章，比如我今天想找一个获取`origin`的变量，找半天没找到，最后自己根据规律随便蒙一个`$http_origin`竟然对了！

以下是我已亲自验证过的（测试版本：`v1.11.8`）：

* `$remote_addr`：客户端IP地址；
* `http_host`：request.getHeader('Host')；
* `http_origin`：request.getHeader('Origin')；
* `http_referer`：request.getReferer('Referer')；

补充：

* `origin`和`host`的区别是，我在A页面调用B页面的接口，那么A是origin，B是host；
* `origin`和`Referer`的区别是，前者是调用页面域名(如http://www.aaa.com )，后者则是不包括哈希的完整URL（如 http://www.aaa.com/index.html）

<!--more-->


以下摘自网络，未亲自验证：

> $args ： #这个变量等于请求行中的参数，同$query_string
$content_length ： 请求头中的Content-length字段。
$content_type ： 请求头中的Content-Type字段。
$document_root ： 当前请求在root指令中指定的值。
$host ： 请求主机头字段，否则为服务器名称。
$http_user_agent ： 客户端agent信息
$http_cookie ： 客户端cookie信息
$limit_rate ： 这个变量可以限制连接速率。
$request_method ： 客户端请求的动作，通常为GET或POST。
$remote_addr ： 客户端的IP地址。
$remote_port ： 客户端的端口。
$remote_user ： 已经经过Auth Basic Module验证的用户名。
$request_filename ： 当前请求的文件路径，由root或alias指令与URI请求生成。
$scheme ： HTTP方法（如http，https）。
$server_protocol ： 请求使用的协议，通常是HTTP/1.0或HTTP/1.1。
$server_addr ： 服务器地址，在完成一次系统调用后可以确定这个值。
$server_name ： 服务器名称。
$server_port ： 请求到达服务器的端口号。
$request_uri ： 包含请求参数的原始URI，不包含主机名，如：”/foo/bar.php?arg=baz”。
$uri ： 不带请求参数的当前URI，$uri不包含主机名，如”/foo/bar.html”。
$document_uri ： 与$uri相同。

参考 https://segmentfault.com/a/1190000002797606 和 http://www.cnblogs.com/AloneSword/archive/2011/12/10/2283483.html

# 转发

例如，将 http://www.test.com 转发到 http://127.0.0.1:6666 ：

```
server {
    listen       80;
    server_name  www.test.com;
    location / {
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        # 代理连接超时设置为10秒钟，默认60秒太久了
        proxy_connect_timeout 10;
        proxy_pass http://127.0.0.1:6666;
    }
}
```

## 跨域配置

如果还需要增加跨域配置，只需要再在上面加上：

```
server {
    listen       80;
    server_name  www.test.com;
    location / {
        add_header 'Access-Control-Allow-Origin' '*';
        add_header 'Access-Control-Allow-Headers' 'Origin, No-Cache, X-Requested-With, If-Modified-Since, Pragma, Last-Modified, Cache-Control, Expires, Content-Type, X-E4M-With';
        add_header 'Access-Control-Allow-Methods' 'POST, GET, OPTIONS';
        # 省略其它部分...
        proxy_pass http://127.0.0.1:6666;
    }
}
```

# 永久跳转

带www的域名自动跳转到没有www的域名，丢弃域名后面的地址。

下面的例子是将 http://www.liuxianan.com 永久跳转到 http://liuxianan.com :

```
server {
    listen       80;
    server_name  www.liuxianan.com liuxianan.cn www.liuxianan.cn;
    location / {
        rewrite  ^(.*)    http://liuxianan.com permanent;
    }
}
```

# 泛二级域名配置

所谓泛二级域名配置，就是将 `*.xxx.com` 根据子域名的不同自动进行不同配置，而不需要手动一个个写。

## 基于转发实现的泛二级域名配置

下面的例子中，将 `http://*.liuxianan.com/` 转发到 `http://127.0.0.1:8080/*/`

```
http {
    include       mime.types;
    default_type  application/octet-stream;
    # 使用变量来构造server地址时必须设置resolver
    resolver 8.8.8.8;
    sendfile        on;
    keepalive_timeout  65;
    server {
        listen       80;
        server_name  www.liuxianan.com liuxianan.cn www.liuxianan.cn;
        location / {
            rewrite  ^(.*)    http://liuxianan.com permanent;
        }
    }
    server {
        listen       80;
        server_name  demo.liuxianan.com;
        location / {
            root   D:\Workspace\github\demo;
            index  index.html index.jsp;
        }
    }
    server {
        listen       80;
        server_name  ~^(.+)?\.liuxianan\.com$;
        location / {
            # 注意这里用localhost的话会报错，必须用127.0.0.1
            proxy_pass http://127.0.0.1:8080/$1$request_uri;
        }
    }
}
```
