# Docker 容器里运行 GUI 图形化界面（以 IDEA 软件为例）

## 1. 环境准备

- Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-77-generic x86_64)
- Docker Docker version 20.10.7, build 20.10.7-0ubuntu1~20.04.1
- apt 设置阿里云的镜像源(/etc/apt/sources.list)如下

~~~ shell
 ## Note, this file is written by cloud-init on first boot of an instance
## modifications made here will not survive a re-bundle.
## if you wish to make changes you can:
## a.) add 'apt_preserve_sources_list: true' to /etc/cloud/cloud.cfg
##     or do the same in user-data
## b.) add sources in /etc/apt/sources.list.d
## c.) make changes to template file /etc/cloud/templates/sources.list.tmpl

# See http://help.ubuntu.com/community/UpgradeNotes for how to upgrade to
# newer versions of the distribution.
deb http://mirrors.cloud.aliyuncs.com/ubuntu/ focal main restricted
deb-src http://mirrors.cloud.aliyuncs.com/ubuntu/ focal main restricted

## Major bug fix updates produced after the final release of the
## distribution.
deb http://mirrors.cloud.aliyuncs.com/ubuntu/ focal-updates main restricted
deb-src http://mirrors.cloud.aliyuncs.com/ubuntu/ focal-updates main restricted

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
## team. Also, please note that software in universe WILL NOT receive any
## review or updates from the Ubuntu security team.
deb http://mirrors.cloud.aliyuncs.com/ubuntu/ focal universe
deb-src http://mirrors.cloud.aliyuncs.com/ubuntu/ focal universe
deb http://mirrors.cloud.aliyuncs.com/ubuntu/ focal-updates universe
deb-src http://mirrors.cloud.aliyuncs.com/ubuntu/ focal-updates universe

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu 
## team, and may not be under a free licence. Please satisfy yourself as to
## your rights to use the software. Also, please note that software in 
## multiverse WILL NOT receive any review or updates from the Ubuntu
## security team.
deb http://mirrors.cloud.aliyuncs.com/ubuntu/ focal multiverse
deb-src http://mirrors.cloud.aliyuncs.com/ubuntu/ focal multiverse
deb http://mirrors.cloud.aliyuncs.com/ubuntu/ focal-updates multiverse
deb-src http://mirrors.cloud.aliyuncs.com/ubuntu/ focal-updates multiverse

## Uncomment the following two lines to add software from the 'backports'
## repository.
## N.B. software from this repository may not have been tested as
## extensively as that contained in the main release, although it includes
## newer versions of some applications which may provide useful features.
## Also, please note that software in backports WILL NOT receive any review
## or updates from the Ubuntu security team.
deb http://mirrors.cloud.aliyuncs.com/ubuntu/ focal-backports main restricted universe multiverse
deb-src http://mirrors.cloud.aliyuncs.com/ubuntu/ focal-backports main restricted universe multiverse

## Uncomment the following two lines to add software from Canonical's
## 'partner' repository.
## This software is not part of Ubuntu, but is offered by Canonical and the
## respective vendors as a service to Ubuntu users.
# deb http://archive.canonical.com/ubuntu focal partner
# deb-src http://archive.canonical.com/ubuntu focal partner

deb http://mirrors.cloud.aliyuncs.com/ubuntu focal-security main restricted
deb-src http://mirrors.cloud.aliyuncs.com/ubuntu focal-security main restricted
deb http://mirrors.cloud.aliyuncs.com/ubuntu focal-security universe
deb-src http://mirrors.cloud.aliyuncs.com/ubuntu focal-security universe
# deb http://mirrors.cloud.aliyuncs.com/ubuntu focal-security multiverse
# deb-src http://mirrors.cloud.aliyuncs.com/ubuntu focal-security multiverse
deb http://archive.ubuntu.com/ubuntu/ bionic universe
~~~

Docker 复用宿主机显示原理

> 原理上可以把docker镜像看做一台没配显示器的电脑，程序可以运行，但是没地方显示。
而linux目前的主流图像界面服务X11又支持 客户端/服务端（Client/Server）的工作模式
只要在容器启动的时候，将 『unix:端口』或『主机名:端口』共享给docker,docker 就可以通过端口找到显示输出的地方，和linux系统共用显示

### 安装 工具

~~~ bash
sudo apt-get install x11-xserver-utils
$ xhost +
~~~

## Docker 镜像制作

Dockerfile

~~~ dockerfile
## 准备运行时框架里的内容
FROM alpine:3.11 as alpine

# 安装JDK, Tomcat, MVN Tool, IDE
COPY tools/* /tmp/ 
RUN mkdir -p /opt/tools \
   && tar xf /tmp/OpenJDK11U.tar.gz -C /opt/tools  \
   && tar xf /tmp/apache-tomcat-8.5.69.tar.gz -C /opt/tools \
   && tar xf /tmp/ideaIU-2020.1.4.tar.gz -C /opt/tools \
   && mkdir -p /opt/shared-lib \
   && mkdir -p /opt/app/bin \
   && mkdir -p /opt/dev \
   && tar xf /tmp/apache-maven-3.8.1-bin.tar.gz -C /opt/dev 

#Java框架的所有依赖库MVN Repository m2包含 settings.xml和repository/ 目录  
COPY m2/ /.m2/

#TODO: add IDEA 集成开发工具

RUN echo "prepare okay!!!"
#TODO IDE

## 制作最终的Java运行时框架
#TODO 使用真正的基础镜像
FROM ubuntu:19.10 

COPY --from=alpine /opt /opt
COPY --from=alpine /.m2 /root/.m2

COPY sources.list /etc/apt/sources.list 
RUN apt update \
apt-get install libxext6 \
libxrender1 \
libxtst6 \
libxi6 \
libfreetype6 \
x11-apps 

ENV JAVA_HOME=/opt/tools/jdk-11
ENV MAVEN_HOME=/opt/dev/apache-maven-3.8.1
ENV PATH=$PATH:$JAVA_HOME/bin:$MAVEN_HOME/bin

# 容器的入口命令为启动tomcat
CMD /opt/tools/idea-IU-201.8743.12/bin/./idea.sh
~~~
