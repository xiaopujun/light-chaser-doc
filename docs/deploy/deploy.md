[LIGHT CHASER](https://github.com/xiaopujun/light-chaser) 提供了 [在线访问](https://xiaopujun.github.io/light-chaser-app/#)
的方式。不过它托管在github Pages上，访问速度可能比较慢。 因此你可以选择本地部署，这样访问速度会快很多。当然你也可以将他用于你的生产环境中。

> 视频教程：
> [Windows部署教程](https://www.bilibili.com/video/BV1ku411c7Q2/?share_source=copy_web&vd_source=ece0559aa5b8c4f5c0d7307cb2b06aac) 
> [Linux部署教程](https://www.bilibili.com/video/BV1kc41117be/?spm_id_from=333.999.0.0&vd_source=74d6c039036796c340cd3b7850ec7244)

## 准备

部署前你需要准备好以下环境：

- 一个静态服务器（如[nginx](https://github.com/xiaopujun/light-chaser/releases)）
- LIGHT CHASER [编译后的源码包](https://github.com/xiaopujun/light-chaser/releases)

> 1. 本文使用nginx进行部署，静态服务没有固定的限制，你可以使用任何你喜欢的静态服务器。
> 2. 如果你要绑定域名，请提前申请好域名，并将域名解析到你的服务器上。相关内容可自行搜索教程

将下载好的nginx和源码包放在同一目录下（可选，方便管理）。如下图，app是源码包中解压后的文件

![deploy](https://picst.sunbangyan.cn/2023/11/05/90cc4a1f190f32b0096fa82cad44a71a.png)

## 配置nginx

找到nginx的配置文件，一般在nginx的安装目录下的conf文件夹中。打开nginx.conf文件，调整如下：

```shell

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
            listen       80;
            server_name  localhost; #你的域名，本地就是localhost。没有映射的自己到本地hosts文件中添加映射

            #root指向你源码包的路径，一般是在部署linux服务器下
    	    root C:\Users\Administrator\Desktop\light-chaser\app;
            index index.html;

            location / {
                try_files $uri $uri/ @router;
                index index.html;
            }

            location @router {
                rewrite ^.*$ /index.html last;
            }

            error_page   500 502 503 504  /50x.html;
            location = /50x.html {
                root   html;
            }
        }
}

```

## 启动nginx

启动nginx，如果没有报错，就说明启动成功了。此时你可以在浏览器中输入你的域名，就可以访问了。

> linux下的部署方式大同小异，不再赘述。
