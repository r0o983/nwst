* 设置主机名

  `hostnamectl set-hostname XXX`

* 封装模板机

  `cd /etc/sysconfig/network-scripts/`

  `vim ifcfg-ens33`

  删除'uuid'这一行

  修改`BOOTPROTO="DHCP"` 为自动获取IP方式

  重启网络服务`systemctl restart network`

## Centos6和Centos7的防火墙以及系统命令的区别

iptables centos6

service 服务启动方式

service start XXservice

firewalld centos7

`systemctl stop firewalld`

`systemctl disable firewalld` 

