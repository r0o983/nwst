# docker常用命令

## docker基础命令

### 帮助命令

```shell
docker version //版本信息
docker info // docker的详细信息
docker 命令 --help //帮助信息
帮助文档地址：https://docs.docker.com/reference/
```

### 镜像相关命令

```shell
docker images 查看所有本机上的所有镜像
repository	 //镜像的仓库源
Tag 	// 镜像的标签
image 	// id 镜像的id
created 	// 镜像的创建时间
size   // 镜像的大小
可选项
-a --all //列出所有镜像
-q --quiet // 只显示镜像的id
```

### 操作命令

>  docker search mysql

  ```
  下载命令
  docker pull mysql
  可选：[:tag]
  
  删除镜像命令
  docker rmi -f $(docker images -aq)   // 删除全部镜像
  docker rmi -f id或者名称 //删除某一个镜像，默认删除最新版的镜像
  docker rmi -f id id 名称 名称 //删除多个镜像，以空格区分
  
  容器命令
  docker pull centos //下载
  docker run [可选参数] images
  参数说明：
  --name="Name" 容器名称 tomcat01 tomcat02,用来区分容器
  -d  //后台方式运行
  -it //使用交互方式运行，进入容器查看内容
  -p // 指定容器的端口-p 8080:8080
  	-p ip:主机端口：容器端口
  	-p 主机端口：容器端口（常用）
  	-p 容器端口
  -P // 随机指定端口
  例子：docker run -it centos /bin/bash
  
  列出所有正在运行的容器
  docker ps //查看当前正在运行的容器
  常用参数
  -a //列出所有运行过的容器
  -n=? // 显示最近的一次容器记录
  -q // 只显示容器的编号
  
  退出容器
  exit  // 停止容器并退出
  ctrl -p -q //容器不停止退出
  
  删除容器
  docker rm 容器id //删除某一个容器，如果无法删除可以先进行停止容器
  docker stop 容器id // 停止正在运行的容器
  docker rm -f $(docker ps -aq) //删除所有容器
  dpcler ps -a -q | xargs docker rm //删除所有容器
  
  启动和停止容器操作
  docker start 容器id	//启动容器
  docker restart 容器id 	//重启容器
  docker stop 容器id	// 停止容器
  docker kill 容器id	//杀掉容器
  
  常用其他命令
  docker run -d 镜像名	// 后台启动容器
  注意：在运行一个空系统时，docker默认会停止服务
  
  查看日志
  docker logs -f -t 容器id // 查看当前容器的日志信息
  docker logs -f -t --tail 10 容器id  //显示最新的10条日志，并实时更新
  
  查看容器的进程信息
  top命令
  docker top 容器id
  
  查看镜像的源数据
  docker inspect 容器id
  
  进入当前正在运行的容器
  #方式1--> 进入容器后开启一个新的终端
  docker exec -it 容器id /bin/bash 	//进入该容器
  #方式2--> 进入容器当前正在运行的终端
  docker attach 容器id //进入容器并进入当前正在运行的bash
  
  拷贝主机的文件到容器内
  docker cp 容器id：容器内路径 目的地主机路径
  例子：
  docker cp 容器id:/home/abc.java /home
  
  测试用例：
  docker run -it --rm tomcat 	// 退出后直接删除该容器
  一般用于测试
  ```

#### 安装可视化面板示例：

```
//安装docker pull elasticsearch

docker run -d --name elasticsearch -p9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.6

docker stats  容器id// 查看当前docker占用状态
参数说明：
-e 环境配置修改
-e ES_JAVA_POTS=""

//安装可视化面板
docker run -d -p8088:9000 \--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer

```

### docker提交镜像

```
docker commit 提交容器成为一个新的镜像
docker commit -m="描述信息" -a="作者信息" 当前容器id 目标镜像名：[tag]
docker commit -m="chanage tomcat" -a="show" a231b23 tomcat:8.1
```

### docker容器数据卷

```
容器的持久化和同步操作
容器间也可以进行数据共享
> 方式1：直接使用命令来挂在 -v
docker run -it -v 主机目录：容器内目录 

安装mysql 并设置持久化数据
docker run -d -p3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 mysql
参数说明：
-e 设置系统配置
-v 挂载目录
```

### 具名挂载和匿名挂载

```
//匿名挂载
docker run -d -P --name nginx01 -v /etc/nginx nginx
参数说明：
-P 随机指定一个端口

使用volume 的情况
docker volume ls

//具名挂载
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx nginx

//查看当前挂载的目录所在位置
docker volume inspect juming-nginx 

如何确定是具名挂载还是匿名挂在，还是指定路径挂载
-v  容器内路径  //匿名挂载
-v  卷名：容器内路径  //具名挂载
-v /宿主机路径：容器内路径  //指定路径挂载

拓展：
通过 -v 容器内路径：ro rw改变读写权限
ro：只读 
rw：读写
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:ro nginx

一旦设置容器权限，容器就只能通过宿主机来进行操作，容器内部无法进行操作
```

## 初识Dockerfile

```
创建一个docker的镜像构建文件
  FROM centos
  VOLUME ["volume01","volume02"]
  CMD echo "---end---"
  CMD /bin/bash

//生成镜像
docker build -f dockfile1 -t hello/centos .

//找到挂载的目录地址
docker inspect a92bce9cd3b3

```

数据卷容器
两个mysql同步数据
docker run -it --name docker01 b59  //启动父容器
docker run -it --name docker02 --volumes-from docker01 b59 //启动子容器

只要通过--volumes-from来进行挂载后就可以同步进行访问

## 重启服务器后需要开启docker进程

> sudo service docker restart 
> 设置开机启动 
> systemctl enable docker

## dockerfile 构建

> FROM ：基础镜像 
> MAINTAINER :指定维护者信息 
> RUN ：需要执行的命令 
> ADD ：copy文件，会自动解压 
> WORKDIR ：工作目录
> VOLUME ： 设置容器卷，挂载位置 
> EXPOSE ：对外指定端口 
> RUN ：执行命令
> CMD ：指定容器启动时运行的命令，只有最后一个会生效，可被替代
> ENTRYPOINT ：指定容器启动时运行的命令，可以追加命令
> ONBUILD ： 当构建对象被继承dockerfile，这个时候就会运行onbuild指令，触发执行 
> COPY ： 类似ADD命令，将文件拷贝到镜像中
> ENV ： 构建时设置环境变量

### dockerfile构建测试

```shell
1.编写配置文件
FROM centos
MAINTAINER Bond

ENV MYPATH /usr/lcoal
WORKDIR $MYPATH

RUN yum -y install vim net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "-----end-----"
CMD /bin/bash

2.创建dockerfile镜像
docker build -f buildocker -t mycentos .
参数说明：
build创建文件
-f 使用哪一个脚本来进行创建dockerfile
-t dockerfile生成后的名称
. 当前路径

docker history 镜像名 //查看当前镜像构建过程
>cmd 和 ENTRYPOINT 区别
cmd 命令在前台输入命令后会进行覆盖当前命令
entrpoint 会在当前正在执行的命令后追加命令
```

### 创建tomcat组合镜像

```
FROM centos
MAINTAINET bond<huanyu.qigongzi@gmail.com>

COPY readme.txt /usr/local/readme.txt

ADD apache-tomcat-10.0.2.tar.gz /usr/local/
ADD jdk-11.0.10_linux-x64_bin.tar.gz /usr/local/

RUN yum -y install vim

ENV MYPATH /usr/local
WORKDRI $MYPATH
ENV JAVA_HOME /usr/local/jdk-11.0.10
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

ENV CATALINA_HOME /usr/local/apache-tomcat-10.0.2
ENV CATALINA_BASH /usr/local/apache-tomcat-10.0.2

ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080

CMD /usr/local/apache-tomcat-10.0.2/bin/startup.sh && -F /url/local/apache-tomcat-10.0.2/bin/logs
/catalina.out

```

### 构建对象

> docker build -t diytomcat -f Dockerfile . 

### 发布镜像到dockerhub

> 注册dockerhub  
> 登录dockerhub  
> 记住dockerhub用户名  
> 在本地使用docker login -u 用户名 -p 密码
> docker login -u bangbangde -p 123456  
> 创建本地镜像版本号：docker tag 镜像id 新镜像名：版本号  
> docker tag 514c9f73cd79 bangbangde/tomcat1.0  
> 提交到docker hub :docker push 新镜像名：版本号  
> docker push bangbangde/tomcat1.0

## docker网络学习  

> 查看当前网络状态

```shell
[root@00-0C-29-F9-BD-60 ~]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:0c:29:f9:bd:60 brd ff:ff:ff:ff:ff:ff
    inet 192.168.8.183/24 brd 192.168.8.255 scope global noprefixroute dynamic ens33
       valid_lft 73611sec preferred_lft 73611sec
    inet6 fe80::f300:bb78:7ac6:906c/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
    link/ether 02:42:6e:f1:30:96 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:6eff:fef1:3096/64 scope link 
       valid_lft forever preferred_lft forever
```

> lo 代表环回网卡 
> ens33代表目前本机ip地址 
> docker 为内部地址 

> 启动一个新的容器 docker -d -P --name tomcat01 tomcat  
> 以交互模式查询当前容器内的地址  
> docker exec -it tomcat01  

```shell
[root@00-0C-29-F9-BD-60 ~]# docker exec -it tomcat01 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
14: eth0@if15: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever

```

> 如何通过容器名来进行ping通网络 > --link  
> docker run -d -P --name tomcat02 tomcat  
> 启动第二个容器来进行link绑定
> docker run -d -P --name tomcat03 --link tomcat02 tomcat 

### 网络模式

> bridge: 桥接模式（docker 默认）  
> none: 不配置网络  
> host: 主机模式，和主机共享同一个网络  
> container:容器网络链接

### 创建docker网络

> docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet  
> 参数说明：

```shell
--driver bridge 桥接模式
--subnet 子网掩码
--gateway 网关路由地址 
```

* 测试

  > docker run -d -P --name tomcat01-mynet --net mynet tomcat  
  > docker run -d -P --name tomcat02-mynet --net mynet tomcat  
  > docker exec -it tomcat01-mynet ip addr(192.168.0.2)  
  > docker exec -it tomcat02-mynet ip addr(192.168.0.3)  
  > docker exec -it tomcat01-mynet ping 192.168.0.3(通)  
  > docker exec -it tomcat02-mynet ping 192.168.0.2(通)  
  > 不需要使用--link，只要使用同一网络即可实现内网互联。

### 将其他网络中的容器连通到创建的网络中  

> docker run -d -P --name tomcat01 tomcat  //创建一个容器
> docker network connect mynet tomcat01 //为该容器添加一个虚拟网络接口  
> 此时已经可以ping通网络了

### 练习：redis集群

> 创建redis脚本

```shell
for port in $(seq 1 6);\
do \
mkdir -p /mydata/redis/node-${port}/conf
touch /mydata/redis/node-${port}/conf/redis.conf
cat <<EOF >/mydata/redis/node-${port}/conf/redis.conf
port 6379
bind 0.0.0.0
cluster-enable yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 172.38.0.1${port}
cluster-announce-port 6379
cluster-announce-bus-port 16379
appendonly yes
EOF
done
```

> 启动redis

```shell
docker run -p6371:6379 -p16371:16379 --name redis-1 -v /mydata/redis/node-1/data:/data -v /mydata/redis/node-1/conf/redis.conf:/etc/redis/redis.conf -d --net redis --ip 172.38.0.11 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

自动循环创建
docker run -p 637${port}:6379 -p1637${port}:16379 --name redis-${port} -v /mydata/redis/node-${port}/data:/data -v /mydata/redis/node-${port}/conf/redis.conf:/etc/redis/redis.conf -d --net redis --ip 172.38.0.1${port} redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf;

```



## docker Compose

> 轻松高效的管理容器，定义运行多个容器
> 使用步骤：
>
> 1.定义应用环境（docker file）  
> 2.定义一个服务（docker-compose.yml）  
> 3.运行docker服务（docker-compose up）  
> 作用：批量容器编排



* docker compose是docker官方开源的项目，需要安装  
  docker.yml 示例

  ```shell
  version: "3.9"  # optional since v1.27.0
  services:
    web:
      build: .
      ports:
        - "5000:5000"
      volumes:
        - .:/code
        - logvolume01:/var/log
      links:
        - redis
    redis:
      image: redis
  volumes:
    logvolume01: {}
  ```

* docker-compose up

> 安装compose(debian)  

1. sudo curl -L "https://github.com/docker/compose/releases/download/1.28.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

> 赋予权限

2. sudo chmod +x /usr/local/bin/docker-compose

> 查看是否安装成功--有正常版本输出即可

3. docker-compose version

> 查看当前网络详细信息  
> docker network inspect composetest_default

yml规则：

```shell
version: "" # 版本
services: # 服务
  服务1：web
    images
    build
    network
  服务2:redis
    ....
  服务3:tomcat
    ...
# 其他服务：网络，挂载卷，全局规则...
volumes:
network:
configs
```

测试安装wordpress on docker  

> mkdir my_wordpress
> cd my_wordpress
> vim docker-compose.yml 

```yml
version: '3.3'

services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
       WORDPRESS_DB_NAME: wordpress
volumes:
    db_data: {}
```

> docker-compose up -d # 从当前目录创建文件  
> docker-compose up # 启动镜像创建容器运行  

当需要项目重新构建时可以使用

> docker-compose up --build

## docker Swarm

### 准备工作

> 准备4台虚拟机，并进行配置（centos7）  

```shell
安装yum-utils软件包（提供yum-config-manager 实用程序）并设置稳定的存储库

sudo yum install -y yum-utils

设置存储库
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
    
安装DOCKER引擎
sudo yum install docker-ce docker-ce-cli containerd.io

启动docker
sudo systemctl start docker

设置开机启动
systemctl enable docker

卸载Docker Engine，CLI和Containerd软件包：（如果有需要）
sudo yum remove docker-ce docker-ce-cli containerd.io
主机上的映像，容器，卷或自定义配置文件不会自动删除。要删除所有图像，容器和卷：
sudo rm -rf /var/lib/docker
```

1. 查看当前的机器所在的内网地址

   ```shell
   ip addr
   
   ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
       link/ether 00:0c:29:a5:e3:89 brd ff:ff:ff:ff:ff:ff
       inet 192.168.8.187/24 brd 192.168.8.255 scope global noprefixroute dynamic ens33
          valid_lft 85167sec preferred_lft 85167sec
       inet6 fe80::c9e9:70e0:90c4:99c5/64 scope link tentative noprefixroute dadfailed 
          valid_lft forever preferred_lft forever
       inet6 fe80::f480:b114:26c3:3a93/64 scope link noprefixroute 
          valid_lft forever preferred_lft forever
          
   ```

### 搭建集群

   ```SHELL
   docker swarm init --advertise-addr 192.168.8.187
   
   Swarm initialized: current node (73fur7s9t0jadqdrtg9egg0rb) is now a manager.
   
   To add a worker to this swarm, run the following command:
   
       docker swarm join --token SWMTKN-1-1yq6464kettqgr689ofodgdznumcozsppx2bccq5o9ngm3wixs-1zfia155s5smfff3ddfm1x7kh 192.168.8.187:2377
   
   To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
   
   ```

初始化节点`docker swarm init ` 

`docker swarm join` 加入节点

   ```SHELL
# 获取令牌--输入命令生获取令牌
docker swarm join-token manager
docker swarm join-token worker
   ```

   查看当前网络节点状态

   >docker node ls

   **注意** ： 需要加入的主节点都需要开启防火墙端口，否则会无法连接

配置步骤：

> 生成主节点init
>
> 加入（管理者、worker）

### Raft协议

> Raft协议：保证大多数节点存活才可以进行使用-- 高可用

**集群环境下最低保证三个节点可用** > 一台管理节点存活

### 体验

使用Swarm来进行docker动态扩缩容



创建服务

> docker service create -p8889:80 --name my-nginx ngin 服务启动--具有扩缩容服务
>
> docker run 容器启动--不具有扩缩容服务

查看服务运行的副本

> docker service ls

新增服务副本-->在任意管理节点键入以下命令---> 动态扩缩容

> docker service update --replicas 3 my-nginx  # 新增三个服务副本
>
> docker service scale my-nginx=10 # 新增副本数到10个副本

删除当前副本

> docker service rm my-gninx

### 概念总结

**swarm** 

集群的管理和编号，docker可以初始化一个swarm集群，其他节点可以加入（管理，工作者）

**Node**

就是一个docker节点，多个节点组成一个网络集群（管理，工作者）

**Service**

任务，可以在管理节点或者工作节点来运行，用户访问

**Task**

容器内的命令，细节任务

![image-20210216191350369](C:\Users\Jerry\Desktop\debian.assets\image-20210216191350369.png)

命令>> 管理 >> api >> 调度 >> 工作节点(创建Task容器) >>





### 无法加入域问题解决办法

   **无法加入的情况下先查看端口是否开启**

   > netstat -lntp

   如果端口开启则查看防火墙是否开启

   > firewall-cmd --query-port=2377/tcp

   如果没有开启则开启防火墙放行

   > firewall-cmd --zone=public --add-port=2377/tcp --permanent

   重启防火墙

   > firewall-cmd --reload

   ```shell
开启端口

[root@centos7 ~]# firewall-cmd --zone=public --add-port=80/tcp --permanent

查询端口号80 是否开启：

[root@centos7 ~]# firewall-cmd --query-port=80/tcp

重启防火墙：

[root@centos7 ~]# firewall-cmd --reload

查询有哪些端口是开启的:

[root@centos7 ~]# firewall-cmd --list-port

命令含义：

--zone #作用域
--add-port=80/tcp #添加端口，格式为：端口/通讯协议
--permanent #永久生效，没有此参数重启后失效

关闭firewall：

systemctl stop firewalld.service #停止firewall

systemctl disable firewalld.service #禁止firewall开机启动
   ```

   



## docker Stack

## docker Secret

## docker Config