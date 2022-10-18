## 1、Docker简介

### Docker是什么？

<img src="https://gitee.com/zhanqingqidev/pic/raw/master/blog/pics/20210218102624.png" alt="img" style="zoom:150%;" />

> Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. With Docker, you can manage your infrastructure in the same ways you manage your applications. By taking advantage of Docker’s methodologies for shipping, testing, and deploying code quickly, you can significantly reduce the delay between writing code and running it in production.（Docker 是一个用于开发、发布和运行应用程序的开放平台。Docker可以帮你将应用程序和基础架构隔离开，从而可以快速交付软件。借助Docker你可以使用管理基础架构平台方式来管理你的应用程序，利用Docker可以快速的进行开发、测试和部署，大大减少产品的交付时间。）

Docker 是对Linux虚拟容器的封装，提供简单易用的容器使用呢接口，它可以将应用程序与该程序的依赖，打包在一个文件里。运行该文件，就会生成一个虚拟容器，程序在这个虚拟机里运行就好像在真实的物理机上运行一样。跨平台不用担心环境不一致问题，可以做到一次编译打包到处运行的效果。

### Dcoker VS LXC (容器虚拟机)

<img src="https://gitee.com/zhanqingqidev/pic/raw/master/blog/pics/20210218102635.png" alt="img" style="zoom:150%;" />

### Docker的优点

（1）、跨平台：一次编译到处运行

（2）、启动快、资源占用少、轻量体积小

（3）、方便管理与部署应用，可动态扩缩容

### Docker要解决什么问题？

（1）、解决环境不一致问题，提供一次性环境：可以将本地运行项目的环境，打包发布到测试或生产服务器。

（2）、提供弹性的云服务：Docker容器可以随开随关，可以很方便的进行动态扩缩容。

（3）、组建微服务架构：通过多容器，一台机器可以跑多个服务，因此在一台机器上可以模拟出微服务架构。

## 2、Docker安装

- 1: 安装必要的一些系统工具

    ```html
    yum install -y yum-utils device-mapper-persistent-data lvm2
    ```

- Step 2: 添加软件源信息

    ```shell
    yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    ```

- Step 3: 更新并安装Docker-CE

    ```shell
    yum makecache fast yum -y install docker-ce
    ```

- Step 4: 开启Docker服务

    ```shell
    service docker start
    ```

- 配置加速镜像，/etc/docker/daemon.json

    ```shell
    mkdir -p /etc/docker
    ```

- 国内镜像加速：阿里云的加速镜像地址需要到 https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors 查看4xi2hgtw

    ```shell
    tee /etc/docker/daemon.json <<-'EOF'
    
    {
    
    ​	"registry-mirrors": ["https://hub-mirror.c.163.com", "https://xxxxxx.mirror.aliyuncs.com"]
    
    }
    
    EOF
    ```

    ```shell
    systemctl daemon-reload #刷新镜像配置
    
    systemctl restart docker #重启docker
    
    docker info #查看docker信息
    ```

## 3、Docker常用命令：

- 查看本机Docker相关信息

    ```shell
    docker -v #查看版本号
    
    docker version #查看版本详细信息
    
    docker info #查看docker详细信息（包括配置信息）
    
    docker inspect #查看docker中的对象信息（镜像或者容器）
    ```

- 本机Docker镜像相关

    ```shell
    docker pull #从镜像仓库（Docker Hub）拉取镜像
    
    docker search #从镜像仓库（Docker Hub）查询镜像
    
    docker images #查看本机上的镜像列表
    
    docker image ls #查看本机上的镜像列表
    
    docker image inspect #查看镜像详细信息
    
    docker save #保存docker中的镜像到本地
    
    docker rmi #删除镜像
    
    docker build -t [镜像名] [上下文路径“.”] #构建镜像，-t 指定创建的目标镜像名，上下文路径“.”指定Dockerfile文件所在目录，可以指定Dokcerfile的绝对路径
    ```

- 本机Docker容器使用

    ``` shell
    docker run -it [镜像名] [命令] ：以终端的交互方式启动一个容器，退出终端后，容器也跟着自动停用
    
    docker run -d -v [本地目录]:[容器目录] -p [主机端口]:[容器端口] [镜像名] ：以后台运行模式启动一个容器，挂载数据卷,并将容器端口与主机端口进行映射
    
    docker ps [-a] : 查看当前正在运行的容器或所有容器
    
    docker start [容器ID] : 启动一个已停止的容器
    
    docker stop [容器ID] : 停止一个容器
    
    docker restart [容器ID] : 重启一个容器
    
    docker attach [容器ID] : 进入后台运行的容器，退出后会导致容器的停止
    
    docker exec -it [容器ID] /bin/bash : 以终端交互的模式进入后台运行的容器（推荐使用，退出容器后不会导致容器的停止）
    
    docker logs -f [容器ID或名字] : 查看容器内部的标准输出；-f 实时自动刷新日志
    
    docker rm -f [容器ID] : 删除容器
    
    docker export|import : 导出导入容器快照
    ```
## 4、Dockerfile指令介绍
    
   Dockerfile是一个用来构建镜像的文本文件，文本内容包含了一条条构建镜像所需的指令和说明。
    
```
    ENV : 设置容器内的环境变量，作用于容器内部的环境变量
    
    ARG : ARG <参数名>[=<默认值>],指定变量，作用于Docerfile中使用的变量
    
    FROM : 指定定制镜像所需要的基础镜像。
    
    MAINTAINER : 指定镜像的维护者。
    
    WORKDIR : 指定镜像构建的当前目录，相当于"cd".
    
    COPY : COPY [--chown=<user>:<group>] ["<源路径1>", ... "<目标路径>"] 从上下文目录中复制文件或者目录到容器里指定路径。
    
    ADD : 复制文件并自动解压（会令镜像的构建缓存失效，导致构建缓慢，不推荐使用）
    
    VOLUME : 挂载数据卷，避免重要的数据，因容器重启而丢失；避免容器不断变大；类似于docker run -v参数
    
    EXPOSE : 声明当前容器会对外暴露的端口，用于与外界进行交互.
    
    RUN : RUN <shell 命令> 在docker build 构建容器时运行相应的指令.
    
    CMD : CMD <shell 命令> 在docker run 启动容器时运行该指令

```
## 5、Docker运行样例

