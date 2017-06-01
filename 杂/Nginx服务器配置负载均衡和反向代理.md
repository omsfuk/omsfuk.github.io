-----------------------
title: 使用Nginx实现负载均衡 + 静态资源分离
tags: nginx
-----------------------

使用Nginx实现负载均衡 + 静态资源分离

配置文件如下：
```
user root;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {

    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    upstream tomcat_novel {
        server localhost:8080 weight=1 max_fails=2 fail_timeout=30s;
    }



    server {
        listen       80;
        server_name novel.knife037.cn;

        location /
        {
            # tomcat_cluster必须和upstream的名称一样
            proxy_pass http://tomcat_novel;
            proxy_next_upstream http_502 http_504 error timeout invalid_header;
            proxy_set_header   REMOTE-HOST $remote_addr;
            proxy_set_header   Host $host;
            proxy_set_header   X-Real-IP $remote_addr;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            client_max_body_size   100m;
            #expires定义用户浏览器缓存的时间为10天            
            # expires 10d;一定不能加这句，否则动态页面会被会被缓存！！！
        }

        location ~* \.(html|htm|gif|jpg|jpeg|bmp|png|ico|txt|js|css|swf|flv|wma|wmv|asf|mp3|mmf|zip|rar)$
        {
            root  /home/omsfuk/resource/Novel;
            expires 10d;
        }

    }

    server {
        listen       80;
        server_name qq.knife037.cn;

        location /
        {
            # tomcat_cluster必须和upstream的名称一样
            proxy_pass http://tomcat_novel;
            proxy_next_upstream http_502 http_504 error timeout invalid_header;
            proxy_set_header   REMOTE-HOST $remote_addr;
            proxy_set_header   Host $host;
            proxy_set_header   X-Real-IP $remote_addr;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            client_max_body_size   100m;
            #expires定义用户浏览器缓存的时间为10天
            # expires 10d;;一定不能加这句，否则动态页面会被会被缓存！！！
        }

        location ~* \.(html|htm|gif|jpg|jpeg|bmp|png|ico|txt|js|css|swf|flv|wma|wmv|asf|mp3|mmf|zip|rar)$
        {
            root  /home/omsfuk/resource/QQ;
            expires 10d;
        }

    }


}

```

一定要用root权限（其他的权限也可以，但是要能够访问静态资源，默认是nobody，无法访问静态资源）开始的时候没有使用`user root`，显示403，查了下日志，发现Permition Denied。改成`root`就好了。

再就是如果之前设置了加上了`10d`的话，需要清一下浏览器缓存。强制刷新好像不怎么有用。。。。
