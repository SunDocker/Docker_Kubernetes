# Docker概述

## 1 Docker概念

开发中不同环境下的软件跨环境迁移问题：

> 代码“水土不服”

<img src="Docker.assets/image-20220718222652346.png" alt="image-20220718222652346" style="zoom:80%;" />

Docker概念：

<img src="Docker.assets/image-20220718223028147.png" alt="image-20220718223028147" style="zoom:80%;" />

- Docker是一个开源的**应用容器引擎**
- 诞生于2013年初，基于Go语言实现，dotCloud公司出品（后改名Docker Inc）
- Docker可以让开发者打包他们的**应用**以及**依赖包**到一个**轻量级、可移植的容器**中，然后发布到任何流行的Linux机器上
- 容器是完全使用沙箱机制，**相互隔离**
- 容器**性能开销极低**

> ==**Docker**是一种容器技术，解决软件跨环境迁移的问题==

## 2 安装Docker

Docker可以运行在在MAC、Windows、CentOS、[UBUNTU](https://so.csdn.net/so/search?q=UBUNTU&spm=1001.2101.3001.7020)等操作系统上

 官网：https://www.docker.com

1.  yum包更新到最新

   ```
   yum update
   ```

2. 安装需要的软件包，yum-util 提供yum-config-manager功能，另外两个是devicemapper驱动依赖的

   ```
   yum install -y yum-utils device-mapper-persistent-data lvm2
   ```

3. 设置yum源

   ```
   yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
   ```

4. 安装docker，出现输入的界面都按 y

   ```
   yum install -y docker-ce
   ```

5. 查看docker版本，验证是否成功

   ```
   docker -v
   ```

## 3 Docker架构

<img src="Docker.assets/image-20220718230534617.png" alt="image-20220718230534617" style="zoom:67%;" />

> 注：
>
> - 这里的本机local host指的就是虚拟机CentOS
> - **image**和**container**的关系就像**类**和**对象**的关系
> - **Docker hub**和**private registry**就像Maven**中央仓库**和**本地仓库**那样

- 镜像（Image）：Docker镜像（Image），就相当于是一个root文件系统。比如官方镜像 ubuntu:16.04就包含了完整的一套Ubuntu16.04最小系统的root文件系统
- 容器（Container）：镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和对象一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等
- 仓库（Repository）：仓库可以看成一个代码控制中心，用来保存镜像







