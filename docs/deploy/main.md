# 开源版部署

## 视频教程

<div style="display: flex;flex-wrap: wrap; justify-content: flex-start; align-items: stretch; ">
    <div style="width: 100%; height:500px; flex-grow: 0;min-width: 100px;margin: 10px;">
         <iframe src="//player.bilibili.com/player.html?isOutside=true&aid=282110710&bvid=BV1kc41117be&cid=1363321229&p=1&autoplay=0" scrolling="no" border="0" frameborder="no" style="width: 100%; height: 100%;" framespacing="0" allowfullscreen="true"></iframe>
    </div>
</div>

## 常规部署

### 前提条件

常规部署前需要自行准备好以下环境：

- JDK17运行环境
- MySQL数据库
- Nginx服务器

### 部署前端

#### 第一步：解压文件

前端部署包light-chaser-pro.0.0.2.zip解压，解压后的文件放在你的自定义目录下，例如`/usr/app` 或者 `D:\app`

#### 第二部：准备nginx

准备好nginx服务器后，将压缩包内的nginx.conf覆盖nginx默认的配置文件，并做如下调整：

> 注：如果熟悉nginx配置可根据自己需求调整配置，覆盖文件不是必选项

```nginx configuration

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
            server_name  替换为自己服务器的ip或者域名;
		
            # 替换为前解压缩文件所在目录，例如上面说的/usr/app 或者 D:\app
    	    root /usr/app;
            index index.html;

            location / {
                try_files $uri $uri/ @router;
                index index.html;
            }

            location ~ /(api|static)/ {
                    proxy_pass 替换为自己后端服务器的ip或者域名:后端服务端口;
                    proxy_set_header Host $host;
                    proxy_set_header X-Real-IP $remote_addr;
                    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                    proxy_set_header X-Forwarded-Proto $scheme;
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

#### 第三步：启动前端服务

使用如下命令启动nginx服务器

```shell
nginx
```

> 注：启动前可使用 `nginx -t` 命令检查配置文件是否正确

## Docker部署

> light chaser的前后端镜像均上传在docker
> hub上，可以通过以下命令拉取镜像。你可以访问[Docekr官网](https://hub.docker.com/search?q=light-chaser)查看并获取docker镜像

> 注：使用docker部署前请确保已经安装docker服务，并熟练使用docker命令

### 部署LIGHT CHASER前端服务

#### 1. 拉取镜像

```shell
docker pull puyinzhen/light-chaser:v1.2.0
```

#### 2. 运行容器

```shell
docker run -v 宿主机nginx配置文件位置:/etc/nginx/conf.d/default.conf:ro -p 80:80 --name light-chaser01 puyinzhen/light-chaser:v1.2.0
```

说明：

1. -v 宿主机nginx配置文件位置:/etc/nginx/conf.d/default.conf:ro
   ：此命令参数用于将宿主机的nginx配置文件映射到容器的/etc/nginx/conf.d/default.conf文件，容器启动时会读取该配置文件。因为前端访问后端服务时，涉及到对应端口的反向代理，因此你可能需要自定义你的nginx配置文件。

2. -p 80:80 ：此命令参数用于将容器的80端口映射到宿主机的80端口，外部网络可以通过访问宿主机的80端口访问容器的服务。可以自行调整

##### 宿主机配置文件内容

```nginx


server {
    listen       80;
    server_name  localhost; # 你的域名或ip

    root /usr/app/light-chaser;
    index index.html;

    location /api {
        proxy_pass http://localhost:8080; # 后端服务地址和端口，这里可以指向你后端docker镜像所映射的ip和端口
    }

    location /static {
        proxy_pass http://localhost:8080; # 后端服务地址和端口，这里可以指向你后端docker镜像所映射的ip和端口
    }

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
```

到此，你已经成功部署了LIGHT CHASER的前端服务

现在你可以通过访问你的宿主机ip:80访问LIGHT CHASER并使用它了