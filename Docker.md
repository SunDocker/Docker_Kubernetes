# 1 Docker概述

## 1.1 Docker概念

开发中不同环境下的**软件跨环境迁移**问题：

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

## 1.2 安装Docker

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

## 1.3 Docker架构

<img src="Docker.assets/image-20220718230534617.png" alt="image-20220718230534617" style="zoom:67%;" />

> 注：
>
> - 这里的本机local host指的就是虚拟机CentOS
> - **image**和**container**的关系就像**类**和**对象**的关系
> - **Docker hub**和**private registry**就像Maven**中央仓库**和**私服**那样

- 镜像（Image）：Docker镜像（Image），就相当于是一个**root文件系统**。比如官方镜像 ubuntu:16.04就包含了完整的一套Ubuntu16.04最小系统的root文件系统

  > 就是安装一个**容器系统**需要的东西

- 容器（Container）：镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和**对象**一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等

- 仓库（Repository）：仓库可以看成一个**代码控制中心**，用来保存镜像

## 1.4 配置Docker镜像加速器

默认情况下，将从docker hub（https://hub.docker.com/）上下载docker镜像，太慢。一般都会配置镜像加速器

USTC：中科大镜像加速器（https://docker.mirrors.ustc.edu.cn）
阿里云
网易云
腾讯云

 在这里我们用阿里云镜像加速器，**登陆阿里云**，进入**控制台**，搜索**容器镜像服务**，找到**镜像加速器**，得到**个人加速地址**

 注意：用阿里云时，每个人加速器地址不同，然后在终端输入下面指令

```c
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://zteks7pj.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

# 2 Docker命令

## 2.1 Docker服务相关命令

启动docker 服务：`systemctl start docker`

停止docker 服务：`systemctl stop docker`

重启docker 服务：`systemctl restart docker`

查看docker 服务状态：`systemctl status docker`

设置开机启动docker：`systemctl enable docker`

禁止开机启动docker：`systemctl disable docker`

## 2.2 Docker镜像相关命令

查看镜像：查看本地所有的镜像

```sh
docker images
docker images -q #查看所有镜像的id
```

搜索镜像：从网络中查找需要的镜像

```c
docker search 镜像名称
```

拉取镜像：从Docker 仓库下载镜像到本地，**镜像名称**格式为`名称:版本号`，如果版本号不指定则是最新的版本。如果不知道**镜像版本**，可以去www.hub.docker.com搜索对应镜像查看

```c
docker pull 镜像名称
```

删除镜像：删除本地镜像

```sh
docker rmi 镜像id|名称号:版本号 #删除指定本地镜像
docker rmi `docker images -q` #删除所有本地镜像
```

> rmi：remove image；
>
> 镜像id通过`docker images`命令查看；
>
> 删除所有本地镜像的命令，其实是将``里面命令的**执行结果当成参数**

## 2.3 Docker容器相关命令

查看容器

```sh
docker ps #查看正在运行的容器
docker ps -a #查看所有容器
```

创建并启动容器

```dockerfile
docker run 参数 版本:版本号 <进入容器的初始化指令>
```

- 参数说明：

  - `-i`：保持容器运行。通常与`-t`同时使用。

    - 加入`it`这两个参数后，容器创建后**自动进入容器**中，退出容器后，容器**自动关闭**

      > 自动关闭是因为没有`-d`

  - `-t`：为容器重新分配一个伪输入**终端**，通常与`-i`同时使用

  - `-d`：以守护（后台）模式运行容器。创建一个容器在**后台运行**。

    - 需要使用`docker exec`进入容器：
      `docker exec -it 容器名 初始化命令`
    - 退出后，**容器仍然不会关闭**

  - `-it`创建的容器一般称为**交互式容器**；`-id`创建的容器一般称为**守护式容器**

  - `--name`：为创建的容器**命名**

  - 后面还会讲解其他参数

- 进入容器的初始化指令

  - centOS镜像默认为`/bin/bash`
  - 如果不是创建后自动进入容器，可以不指定该指令


进入容器

```sh
docker exec -it 容器名 初始化命令
```

停止容器

```sh
docker stop 容器名称
```

启动容器

```sh
docker start 容器名称
```

删除容器：如果容器是**运行状态**则删除失败，需要停止容器参能删除

```sh
docker rm 容器名称
```

> 删除所有容器：
>
> ```sh
> docker rm `docker ps -ap`
> ```

查看容器信息

```sh
docker inspect 容器名称
```

# 3 Docker容器的数据卷

## 3.1 数据卷的概念及作用

### 3.1.1 数据卷概念

<u>思考</u>：

- Docker容器删除后，在容器中产生的数据还在吗？

  - 不在了，容器都没了，其中的数据也消失了
  - 这样很不安全

  <img src="Docker.assets/image-20220726090545884.png" alt="image-20220726090545884" style="zoom:67%;" />

- Docker容器和外部机器可以直接交换文件吗？

  - 不可以
  - 但宿主机和外部机器确实可以

  <img src="Docker.assets/image-20220726090717330.png" alt="image-20220726090717330" style="zoom:67%;" />

- 容器之间想要进行数据交互？

  <img src="Docker.assets/image-20220726090753662.png" alt="image-20220726090753662" style="zoom:87%;" />

> 要解决以上问题，就需要用到**数据卷**

**<u>数据卷</u>**：

- 数据卷是**宿主机**中的一个**目录或文件**

  > 当然，需要一个**挂载**操作
  >
  > <img src="Docker.assets/image-20220726090940051.png" alt="image-20220726090940051" style="zoom:67%;" />
  >
  > 这样，上面的三个问题就解决了

  > 容器**数据的持久化**

- 当容器目录和数据卷目录绑定后，对方的修改会立即同步

- 一个数据卷可以被多个容器同时挂载

  <img src="Docker.assets/image-20220726092934953.png" alt="image-20220726092934953" style="zoom:67%;" />

- 一个容器可以被挂载多个数据卷

### 3.1.2 数据卷作用

- 容器数据持久化
- 外部机器和容器间接通信
- 容器之间数据交换

## 3.2 配置数据卷

创建启动容器时，使用`-v 参数`设置数据卷：

```sh
docker run ... -v 宿主机目录(或文件):容器内目录(文件) ...
```

- 冒号两边不能有空格

- 目录必须是绝对路径

- 如果目录不存在，会自动创建

- 可以挂载多个数据卷

  > 多写几个`-v 参数`就行，当然最好别都往一个目录下挂载，比较乱
  >
  > > 貌似这样也不允许？没试过

## 3.3 配置数据卷容器

<u>思考</u>：

- 多容器进行数据交换问题

  - 刚刚就讲过一种方式了，多个容器挂载同一个数据卷即可

    - 但这种方式操作比较麻烦

  - 还有第二种方式，就是**数据卷容器**

    <img src="Docker.assets/image-20220726093204563.png" alt="image-20220726093204563" style="zoom:67%;" />

    > 这样就算删除c3容器，c1和c2也会保持挂载状态 

<u>配置</u>：

1. 创建并启动一个容器，使用`-v 容器中的目录`设置数据卷

   - 举例：`docker run -it --name=c3 -v /volume centos:7 /bin/bash`

     > `volume`不是固定的名字，可以改的

   - docker会自动在宿主机中分配一个目录当作数据卷

     > 可以使用`docker inspect 容器名`查看分配到的目录，找到`Mounts`即可

2. 创建并启动容器，使用`--volumes-from 参数`设置数据卷容器

   - 举例：
     `docker run -it --name=c1 --volumes-from c3 centos:7 /bin/bash`
     `docker run -it --name=c2 --volumes-from c3 centos:7 /bin/bash`

> 数据卷小结：
>
> - 数据卷概念
>   - 宿主机的一个目录或文件
> - 数据卷作用
>   - 容器数据持久化
>   - 客户端和容器数据交换
>   - 容器之间数据交换
> - 数据卷容器
>   - 创建一个容器，挂载一个目录，让其他容器继承自该容器（`--volume-from`）
>   - 通过简单方式实现数据卷配置

# 4 Docker应用部署

## 4.1 MySQL部署

案例：需求

- 在Docker容器中部署MySQL，并通过外部mysql客户端操作MySQL Server

案例：实现步骤

- 搜索mysql镜像
- 拉取mysql镜像
- 创建容器
- 操作容器中的mysql

问题：外部机器怎么访问容器中的MySQL服务？

- 容器内的网络服务和外部机器不能直接通信

- 外部机器和宿主机可以直接通信

- 宿主机和容器可以直接通信

- 当容器中的网络服务需要被外部机器访问时，可以将**容器中提供服务的端口**映射到宿主机的端口上。外部机器访问宿主机的该端口，从而间接访问容器中的服务

  <img src="Docker.assets/image-20220726095812787.png" alt="image-20220726095812787" style="zoom:67%;" />

- 这种操作称为：**端口映射**

  > 类似数据卷的思想

  - 相关命令见下面的操作步骤

具体操作步骤：

1. 搜索mysql镜像

   ```sh
   docker search mysql
   ```

2. 拉取mysql镜像

   ```sh
   docker pull mysql:5.6
   ```

3. 创建容器，设置端口映射、目录映射

   ```sh
   # 在/root目录下创建mysql目录用于存储mysql数据信息
   mkdir ~/mysql
   cd ~/mysql
   ```

   ```sh
   docker run -id \
   -p 3307:3306 \
   --name=c_mysql \
   -v $PWD/conf:/etc/mysql/conf.d \
   -v $PWD/logs:/logs \
   -v $PWD/data:/var/lib/mysql \
   -e MYSQL_ROOT_PASSWORD=123456 \
   mysql:5.6
   ```

   - 参数说明：

     - `-p 3307:3306`：将容器的3306端口映射到宿主机的3307端口

       > 当然，一般情况下，都是映射到**相同的端口号**下

     - 三个`-v $PWD/...`：挂载**配置目录、日志目录和数据目录**

       > 环境变量`$PWD$`在这个场景下就相当于`/root/mysql`
       >
       > > 因为前面已经`cd ~/mysql`了

     - `-e MYSQL_ROOT_PASSWORD=123456`：初始化root用户的密码

       > `e`：`env`
       >
       > `MYSQL_ROOT_PASSWORD`是MySQL的环境变量

4. 这样进入容器之后可以通过终端操作， 也可以通过宿主机客户端（比如sqlyog）访问

## 4.2 Tomcat部署

案例：需求

- 在Docker容器中部署Tomcat，并通过外部机器访问Tomcat部署的项目

案例：实现步骤

- 搜索tomcat镜像
- 拉取tomcat镜像
- 创建容器
- 部署项目
- 测试访问

具体操作步骤：

1. 搜索tomcat镜像

   ```sh
   docker search tomcat
   ```

2. 拉取tomcat镜像

   ```sh
   docker pull tomcat
   ```

3. 创建容器，设置端口映射、目录映射

   ```sh
   #在/root目录下创建tomcat目录用于存储tomcat数据信息
   mkdir ~/tomcat
   cd tomcat
   ```

   ```sh
   docker run -id --name=c_tomcat \
   -p 8080:8080 \
   -v $PWD:/usr/local/tomcat/webapps \
   tomcat
   ```

4. 在tomcat文件夹下创建项目，就可以访问了

   > 没有默认页面，需要自己创建一个项目文件夹，然后里面给个`index.html`

## 4.3 Nginx部署

案例：需求

- 在Docker容器中部署Nginx，并通过外部机器访问Nginx

案例：实现步骤

- 搜索Nginx镜像
- 拉取Nginx镜像
- 创建容器
- 测试访问

具体操作步骤：

1. 搜索nginx镜像

   ```sh
   docker search nginx
   ```

2. 拉取nginx镜像

   ```sh
   docker pull nginx
   ```

3. 创建容器，设置端口映射、目录映射

   ```sh
   #在/root目录下创建nginx目录用于存储nginx数据信息
   mkdir ~/nginx
   cd ~/nginx
   mkdir conf
   cd conf
   # 在~/nginx/conf下创建nginx.conf文件，粘贴下面内容
   vim nginx.conf
   ```

   ```nginx
   user nginx;
   worker_processes 1;
   
   error_log 	/var/log/nginx/error.log warn;
   pid 		/var/run/nginx.pid;
   
   events {
       worker_connections 1024;
   }
   
   http {
       include			/etc/nginx/mime.types;
       default_type 	application/octet-stream;
       
       log_format main	'$remote_addr - $remote_user [$time_local] "$request" '
           			'$status $body_bytes_sent "$http_referer" '
           			'"$http_user_agent" "$http_x_forwarded_for"';
       
       access_log		/var/log/nginx/access.log	main;
       
       sendfile 		on;
       #tcp_nopush		on;
       
       keepalive_timeout	65;
       
       #gzip	on;
       
       include			/etc/nginx/conf.d/*.conf;
   }
   ```

   ```shell
   cd .. # 要先回到nginx目录
   docker run -id --name=c_nginx \
   -p 80:80 \
   -v $PWD/conf/nginx.conf:/etc/nginx/nginx.conf \
   -v $PWD/logs:/var/log/nginx \
   -v $PWD/html:/usr/share/nginx/html \
   nginx
   ```

   - 参数说明
     - 注意这里有一个文件映射，`$PWD/conf/nginx.conf`要提前准备好
     - html目录就是存放资源的目录

4. 在html目录中创建`index.html`文件，就可以使用外部机访问nginx了（默认端口就是80）



## 4.4 Redis部署





# 5 Dockerfile










