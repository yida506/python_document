### Docker

#### Docker windows desktop 安装
下载最新版,安装对应的linux镜像

#### Docker desktop 目录修改

> 先创建一个文件夹叫docker
```shell script
# 导出 docker-desktop
wsl --export docker-desktop "D:\\docker\\docker-desktop.tar"
# 注销 docker-desktop
 wsl --unregister docker-desktop
# 导入 docker-desktop
wsl --import docker-desktop "D:\\docker\\wsl" "D:\\docker\\docker-desktop.tar" --version 2

# 导出 docker-desktop-data
wsl --export docker-desktop-data "D:\\docker\\docker-desktop-data.tar"
# 注销 docker-desktop-data
 wsl --unregister docker-desktop-data
# 导入 docker-desktop
wsl --import docker-desktop-data "D:\\docker\\wsl" "D:\\docker\\docker-desktop-data.tar" --version 2
```




### docker 部署demo
#### python 项目打包
##### rquirements生成
```shell
# 安装
python3 -m pip install pipreqs
# 在当前目录生成
pipreqs . --encoding=utf8 --force
```

#### dockerfile生成
```shell
touch Dockerfile.dockerfile
```

然后在里面写入
```shell
# syntax=docker/dockerfile:1

FROM python:3.8-slim-buster

WORKDIR /app

COPY requirements.txt requirements.txt
RUN pip3 install -r requirements.txt

COPY . .

CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0"]
```
然后运行
```shell script
docker build --tag python-docker .
```

### vue项目打包
```shell script
# 打包本地vue项目 会生成一个dist文件夹
npm run build
```
> 新建一个目录 存放disk文件夹,在该文件夹下创建Dockerfile文件以及nginx.conf
Dockerfile:
```text
worker_processes  1;
events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;
    keepalive_timeout  65;

    server {
        #这里也稍微进行了修改，表示前端部署到容器中的端口，在docker部署时端口对应这里即可。
        listen       80;
        server_name  localhost;

        location /proxy {
            root /usr/share/nginx/html/;
            proxy_pass http://host.docker.internal:8079;
            rewrite "^/proxy/(.*)$" /$1 break;
        }
        location / {
            root   /usr/share/nginx/html/;
            try_files $uri $uri/ /html/index.html last;
            index  index.html index.htm;
        }
    }
}


```
nginx.conf
```text
#从docker官网拉取标准的nginx镜像,我们需要基于标准的nginx镜像制作自己的镜像
FROM nginx
#拷贝当前目录的文件到指定文件夹下，改文件夹为镜像中的文件夹
COPY ./dist  /usr/share/nginx/html
#拷贝nginx.conf文件到镜像下，替换掉原有的nginx.conf
COPY ./nginx.conf /etc/nginx/nginx.conf
#输出完成标志
RUN echo


```
然后直接运行
```shell script
#-t指定一个镜像的名字，譬如 nginx-image，注意最后的‘.’不能省略
docker build -t nginx-image .
```

```shell script
# -p ：配置端口映射，格式是外部访问端口：容器内端口
# -d ：后台运行  
# --name : 给容器取名
# docker run -p 8089:80 -d --name [容器名字][镜像名字]
docker run -p 8099:80 -d --name vue-project nginx-image
```


### 容器推送 
##### 本地打包为tar 然后直接上传
```shell script
# 本地端
docker save -o centos.tar centos

# 服务器端
docker load -i  centos.tar
```

#### 运行
```shell script
# 最后一个指代的是包名
docker run -d -p 5000:5000 --name rest-server python-docker
```



