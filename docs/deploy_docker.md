> light chaser的前后端镜像均上传在docekr
> hub上，可以通过以下命令拉取镜像。你可以访问[Docekr官网](https://hub.docker.com/search?q=light-chaser)查看并获取docker镜像

> 注：使用docker部署前请确保已经安装docker服务，并熟练使用docker命令

## 部署LIGHT CHASER后端服务

### 1. 拉取镜像

```shell
docker pull puyinzhen/light-chaser-server:1.0.0
```

### 2. 运行容器

```shell
#创建docker网桥，链接docker容器网络和宿主机网络
docker network create light_chaser_server_network
#运行容器
docker run -v 替换为宿主机配置文件目录:/config -v 替换为宿主机静态资源目录:/source -e SPRING_CONFIG_LOCATION=/config/application.yml --network light_chaser_server_network  -p 8080:8080 --name light-chaser-server01  puyinzhen/light-chaser-server:1.0.0
```

说明：

1. -v 替换为宿主机配置文件目录:/config
   ：此命令参数用于将宿主机的配置文件目录映射到容器的/config目录，容器启动时会读取该目录下的配置文件。springboot应用的application.yml配置可以放置在对应目录下，容器启动时会读取该配置文件。你可以在设立设置并链接你自己的MySQL数据库

2. -v 替换为宿主机静态资源目录:/source ：此命令参数用于将宿主机的静态资源目录映射到容器的/source目录，服务启动后，产生的静态资源文件（比如上传的图片）会存放在该目录下。

3. -p 8080:8080 ：此命令参数用于将容器的8080端口映射到宿主机的8080端口，外部网络可以通过访问宿主机的8080端口访问容器的服务。可以自行调整

4. --name light-chaser-server01 ：此命令参数用于指定容器的名称，可以自行调整，不写默认为随机生成的名称。

### 3. 应用的配置文件

你需要在上面提到的配置文件目录下创建application.yml文件，这样你就可以完全自定义你自己的MySQL数据库服务了。配置文件内容如下：

> 注：请在你自己的数据库中建立一个名为light_chaser_server的数据库实例

```yml
spring:
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    druid:
      driver-class-name: com.mysql.jdbc.Driver
      url: jdbc:mysql://使用宿主机的ip:3306/light_chaser_server?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf-8
      username: 使用你自己的mysql用户名
      password: 使用你自己的mysql密码


# mybatis-plus配置
mybatis-plus:
  type-aliases-package: com.dagu.lightchaser.entity
  global-config:
    db-config:
      logic-delete-field: deleted
      logic-delete-value: 1
      logic-not-delete-value: 0
  configuration:
    map-underscore-to-camel-case: true
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl


light-chaser:
  project-resource-path: /source # 项目资源路径
  source-image-path: /images/ # 源图片路径
  cover-path: /covers/ # 封面路径
  migration: # 是否自动跑sql脚本
    enable: true

#logging:
#  config: classpath:log4j2-spring.xml
#  level:
#    cn.jay.repository: trace

```

到此，你已经成功部署了LIGHT CHASER的后端服务

## 部署LIGHT CHASER前端服务

### 1. 拉取镜像

```shell
docker pull puyinzhen/light-chaser:v1.2.0
```

### 2. 运行容器

```shell
docker run -v 宿主机nginx配置文件位置:/etc/nginx/conf.d/default.conf:ro -p 80:80 --name light-chaser01 puyinzhen/light-chaser:v1.2.0
```

说明：

1. -v 宿主机nginx配置文件位置:/etc/nginx/conf.d/default.conf:ro
   ：此命令参数用于将宿主机的nginx配置文件映射到容器的/etc/nginx/conf.d/default.conf文件，容器启动时会读取该配置文件。因为前端访问后端服务时，涉及到对应端口的反向代理，因此你可能需要自定义你的nginx配置文件。

2. -p 80:80 ：此命令参数用于将容器的80端口映射到宿主机的80端口，外部网络可以通过访问宿主机的80端口访问容器的服务。可以自行调整

#### 宿主机配置文件内容

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