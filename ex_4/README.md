# Ex4

* Docker实践

## Docker安装部署

* 系统环境：Ubuntu 20.04

```bash
sudo apt install docker-compose
```

```log
user@ubuntu:~$ sudo docker version
Client:
 Version:           20.10.12
 API version:       1.41
 Go version:        go1.16.2
 Git commit:        20.10.12-0ubuntu2~20.04.1
 Built:             Wed Apr  6 02:14:38 2022
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server:
 Engine:
  Version:          20.10.12
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.16.2
  Git commit:       20.10.12-0ubuntu2~20.04.1
  Built:            Thu Feb 10 15:03:35 2022
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.5.9-0ubuntu1~20.04.1
  GitCommit:        
 runc:
  Version:          1.1.0-0ubuntu1~20.04.1
  GitCommit:        
 docker-init:
  Version:          0.19.0
  GitCommit:        
user@ubuntu:~$ 
```

## Docker pull 拉取镜像实现服务

* 执行以下操作

```log
user@ubuntu:~$ sudo docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
927a35006d93: Pull complete 
fc3910c70f9c: Pull complete 
e11bfbf9fd54: Pull complete 
fbb8b547daa2: Pull complete 
0f1992aeebd8: Pull complete 
f929dacee378: Pull complete 
Digest: sha256:0d17b565c37bcbd895e9d92315a05c1c3c9a29f762b011a10c54a66cd53c9b31
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
user@ubuntu:~$ sudo docker run -itd -p 8080:80 nginx
8dd5ee0bcb9fdc21f256cb031d1a3bb0c83b53e019a56dcd342a7746470e0c31
user@ubuntu:~$ sudo docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                  NAMES
8dd5ee0bcb9f   nginx     "/docker-entrypoint.…"   12 seconds ago   Up 11 seconds   0.0.0.0:8080->80/tcp   hungry_pike
user@ubuntu:~$ sudo docker exec -it 8dd5 /bin/bash
root@8dd5ee0bcb9f:/# echo "<stu_num> <stu_name>" > /usr/share/nginx/html/stu.html 
root@8dd5ee0bcb9f:/# 
```

* 使用浏览器访问```http://localhost:8080/stu.html```即可

## Dockerfile和Docker build定制

* 创建目录并编写Dockerfile

```bash
mkdir mytomcat
cd mytomcat
vi Dockerfile
```

* 输入以下内容

```dockerfile
FROM ubuntu:20.04
RUN sed -i 's/ports.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list
RUN apt update
RUN apt install -y tzdata
RUN echo "Asia/Shanghai" > /etc/timezone \
    && dpkg-reconfigure -f noninteractive tzdata
RUN apt install -y tomcat9
ENV CATALINA_BASE /var/lib/tomcat9
ENV CATALINA_HOME /usr/share/tomcat9
ENV CATALINA_TMPDIR /tmp
ENV JAVA_OPTS -Djava.awt.headless=true
RUN /usr/libexec/tomcat9/tomcat-update-policy.sh 

EXPOSE 8080
CMD ["/usr/libexec/tomcat9/tomcat-start.sh"]
```

* 构建镜像并启动

```bash
docker build -t mytomcat
docker run -itd -p 8080:8080 mytomcat
```

* 使用浏览器访问```http://localhost:8080```

```log
It works !
If you're seeing this page via a web browser, it means you've setup Tomcat successfully. Congratulations!
...以下省略若干行...
```
