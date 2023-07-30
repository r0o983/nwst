# wordpress 安装记录

## 安装基础环境

* 系统选择:谷歌云-->centos7

* 软件安装:

  * 安装apache,在centos系统下为httpd

    * `sudo yum install httpd`

  * 安装mysql,软件源在系统下替换为mariadb

    * `sudo yum install mariadb-server`

      ```shell
      # 为了提高数据库服务器的安全性，建议您运行 MariaDB 预装的安全脚本。此脚本将删除一些不安全的默认设置并锁定对您的数据库系统的访问。
      
      # 通过运行启动交互式脚本：
      sudo mysql_secure_installation
      
      # 根据提示进行设置即可,如果忘了咋设置的,重新运行命令即可.
      ```

  * 创建数据库

    * `CREATE DATABASE wordpress;`
    * `GRANT ALL PRIVILEGES ON wordpress.* TO 用户名@localhost IDENTIFIED BY '用户的密码';`

  * 刷新数据库权限

    * `FLUSH PRIVILEGES;`
      * `退出使用刚才设置的用户名和密码进行测试登陆` -->

  * 安装php

    * 这一步可以省略,因为一会还需要升级php版本`sudo yum install php php-mysql`
    * 重启apache web服务器启用安装的php`sudo systemctl restart httpd.service`

## 安装WordPress

* 创建mysql数据库

  ```shell
  # 登陆
  mysql -u root -p 
  
  # 创建数据库
  create database wordpress
  
  # 创建用户
  CREATE USER 用户名@localhost IDENTIFIED BY '密码';
  
  # 创建用户以及给用户对应数据库的权限
  GRANT ALL PRIVILEGES ON wordpress.* TO 用户名@localhost IDENTIFIED BY '密码';
  
  # 刷新数据库权限
  FLUSH PRIVILEGES;
  
  ```

* 安装WordPress

  ```shell
  # 安装PHP
  sudo yum install php-gd
  
  # 重启apache服务
  sudo service httpd restart
  
  # 下载wordpress源码
  cd ~
  wget http://wordpress.org/latest.tar.gz
  
  # 解压
  tar xzvf latest.tar.gz
  
  # 将权限和组权限都分配给apache,Web服务器将能够创建和修改WordPress文件，并且还允许我们将内容上传到服务器。
  sudo chown -R apache:apache /var/www/html/*
  ```



## 配置WordPress

* 进入apache跟目录

  * `cd /var/www/html`

* WordPress 依赖的主要配置文件称为`wp-config.php`. 默认情况下包含一个与我们需要的设置大部分匹配的示例配置文件。我们所要做的就是将其复制到默认配置文件位置，以便 WordPress 能够识别和使用该文件：

  * `cp wp-config-sample.php wp-config.php`

* 编辑文件:

  * `vim wp-config.php`

  * ```shell
    # 修改如下三处
    // ** MySQL settings - You can get this info from your web host ** //
    /** The name of the database for WordPress */
    define('DB_NAME', '数据库名');
    
    /** MySQL database username */
    define('DB_USER', '数据库用户名');
    
    /** MySQL database password */
    define('DB_PASSWORD', '数据库密码');
    ```

* 基本上安装到此结束; 打开当前IP后会默认跳转到wordpress配置页



## 错误处理

* 进入配置页后如果出现PHP版本不匹配的问题,则需要修改PHP的对应版本号

```shell
# 添加源 Remi源:
yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm

# 安装yum-utils (yum-config-manager):
yum install yum-utils

# 配置源：首先禁用当前的php源并启用需要安装的php7.4的源
yum-config-manager --disable 'remi-php*'
yum-config-manager --enable   remi-php74

# 执行Update更新PHP7.4
yum update

# 启动php7.4并设置开机自启动
#重启php-fpm
systemctl restart php74-php-fpm
#添加自动启动
systemctl enable php74-php-fpm
#查看php7.4的安装路径
whereis php
```