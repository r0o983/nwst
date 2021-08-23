# docker搭建nextcloud概述

## 在宿主机中安装docker（mac）

`hub.docker.com/_/nextcloud/`

## 拉取镜像（mysql）

配置mysql并设置开机启动，进行持久化数据



## 拉取镜像（nextcloud）

`docker pull nextcloud`

* 启动镜像
  * 参数说明：-d 后台运行
    * -d 后台运行
    * --restart=always：Docker 重启的时候容器也会重启
    * --name nextcloud：命名容器的 name 为 nextcloud，可以替代容器 id 使用
    * -p 80:80：将容器的 80 端口映射到服务器的 80 端口
    * nextcloud：要启动的镜像名称

```
docker run -d --restart=always --name nextcloud -p 80:80 nextcloud
```

* 打开浏览器输入地址+端口号进行访问

  `192.168.1.：80`

* 设置用户名以及密码

  * 

