安装配置wordpress

1. 安装docker略

2. 下载centos镜像 

   > docker pull centos

3. 启动docker镜像

   > docker run -itd --name wordpress centos /bin/bash

4. 进入容器

   > docker attach wordpress /bin/bash

5. 更新系统

   > yum update -y

6. 安装wget并下载wordpress

   > yum install wget -y && wget https://wordpress.org/latest.zip

7. 