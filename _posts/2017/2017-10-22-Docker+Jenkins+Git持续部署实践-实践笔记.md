---
title: Docker+Jenkins+Git持续部署实践
description: Docker+Jenkins+Git持续部署实践.
categories:
 - 实践笔记
tags: Docker, Jenkins, Git
---

# Docker+Jenkins+Git持续部署实践

部署Docker+Jenkins+Git持续集成方案，高效协作，自动化部署。

## 准备

- **在VMware Workstation Pro 上 安装 CenOS 7 系统**
- **保证 CenOS 7 系统可以访问网络**
- **更新 CenOS 7 系统**

更新 CenOS 7 系统

```sh
# 更新系统
sudo yum update
```

## Docker 安装

查看 `CentOS` 内核版本,保证内核版本至少是3.10以上

```sh
# 查看内核版本
[vimx86@galsang ~]$ uname -r
3.10.0-693.2.2.el7.x86_64
```

安装依赖包，由于 CentOS7 系统是最小化安装:

```sh
# 安装依赖软件
yum install deltarpm
```

执行docker安装脚本:

```sh
curl -sSL https://get.docker.com/ | sh
```

启动docker服务:

```sh
sudo service docker start
```

确认docker安装成功:
```sh
[vimx86@galsang ~]$ sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
5b0f327be733: Pull complete 
Digest: sha256:07d5f7800dfe37b8c2196c7b1c524c33808ce2e0f74e7aa00e603295ca9a0972
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://cloud.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/
```

卸载 `Docker`:

```sh

# 查看
yum list installed | grep docker 

# 卸载
yum -y remove docker.xxx.x86_64

# 删除
rm -ef /var/lib/docker

```

## Official Jenkins Docker image 安装

Official Jenkins Docker image Install:

```yml
sudo docker pull jenkins
```

How to use this image:

> 这种方式启动会存储数据，-t 标示以后台模式运行，-p标示容器和宿主服务器之间的开放端口号 
-v表示需要将本地哪个目录挂载到容器中，格式：-v <宿主机目录>:<容器目录> 

```yml

# 查看Jenkins容器的当前用户
[vimx86@galsang var]$ sudo docker run -ti --rm --entrypoint="/bin/bash" jenkins -c "whoami && id"  
[sudo] vimx86 的密码：
jenkins
uid=1000(jenkins) gid=1000(jenkins) groups=1000(jenkins)

# 授权
[vimx86@galsang var]$ sudo chown -R 1000 jenkins_home/

# 启动 Jenkins Docker image 容器
sudo docker run -t  -p 8080:8080 -p 50000:50000  -v /var/jenkins_home:/var/jenkins_home --name jenkins jenkins
```

容器启动后自动生成Jenkins账号密码：

```sh
 *************************************************************
 *************************************************************
 *************************************************************
 
 Jenkins initial setup is required. An admin user has been created and a password generated.
 Please use the following password to proceed to installation:
 
 3209acfbbcfc4346831436eb6df301a6
 
 This may also be found at: /var/jenkins_home/secrets/initialAdminPassword
 
 *************************************************************
 *************************************************************
 *************************************************************
```

## Jenkins 配置

### `JDK` 安装与配置

jdk 安装：

```sh
yum install -y java-1.8.0-openjdk*
```

### `Maven` 安装与配置

maven 安装：

```sh
yum -y install maven
```

### `Git` 安装与配置

git 安装：

```sh
yum -y install git
```



## 参考资料

1. [红帽RHEL安装docker容器引擎](http://www.docker.org.cn/book/install/install-docker-on-rhel-29.html)
2. [Cento系统下docker的安装与卸载](http://blog.csdn.net/a527219336/article/details/50800181)
3. [Official Jenkins Docker image](https://hub.docker.com/_/jenkins/)
4. [Docker Volume 之权限管理（一）](http://blog.csdn.net/yulei_qq/article/details/53228601)
5. [使用Jenkins进行持续集成](https://www.liaoxuefeng.com/article/001463233913442cdb2d1bd1b1b42e3b0b29eb1ba736c5e000)
6. [Official Jenkins Docker image](https://hub.docker.com/_/jenkins/)

