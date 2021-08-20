# 记录一次pve安装

下载地址:

https://pve.proxmox.com/wiki/Downloads

下载最新版本即可

**下载后将镜像写入到u盘，执行安装即可**，此处不进行赘述

## 安装桌面

`` apt install -y ifupdown2 wpasupplicant iw net-tools wireless-tools tasksel``

执行``tasksel``

桌面环境N选一

### 安装语言包

``apt-get install -y locales``

#### 配置语言包

``dpkg-reconfig locales``

### 配置中文输入法

``apt install -y ibus ibus-pinyin``

**一切配置完成后使用``reboot``重启即可使用**

## 下载lxc容器镜像

更新：pveam update

查看可用镜像：pveam available

下载镜像：pveam download local xxxxxxxx  local为存储位置

**默认存储路径为：/var/lib/vz/template/cache/**

查看下载镜像：pveam list local 

## 开启远程桌面

``sudo apt install xrdp `` 安装后该服务会默认进行启动，使用``systemctl status xrdp``查看状态

使用windows默认远程工具即可连接

## 安装zsh

`sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`

## 安装frp并设置开机自启动

`https://github.com/fatedier/frp/releases` 下载适合自己系统的版本

服务器端使用frps进行启动，客户端使用frpc。

`./frps -c ./frps.ini ` 

配置参数.`frps.ini ` 

服务器端

```
由于我用不到web服务，所以此处暂时不做记录
authentication_method = token \\创建一个身份验证
token = ^&*&*&*%%^ 		\\ 此处可以瞎几把写，客户端需保持和服务端一致即可。
```

将`systemd` 文件夹内的frps.server 存放到`/lib/systemd/system/`下

将`frps` 移动到 `/usr/bin/`中

创建一个新的文件夹`mkdir /etc/frp/` ，将`frps.ini` 移动到该目录下即可

设置开机自启动

`systemctl enable frps.server`

**frpc与frps是一样的设置方法**

## 搭建代码服务器

**~~具体开发环境不进行赘述~~** 只记录使用vsde进行配置

安装vs扩展`remote ssh`

添加服务器

配置无密码登录环境

生成密钥：ssh-keygen -t rsa -C "x0x0@xx.com"

使用网络拷贝：`scp ~/.ssh/id_rsa.pub username@11.11.11.11:~/.ssh/authorized_keys`

将本地的密钥传输到服务器中

注意：如果服务器本身已有`authorized_keys`文件则需要追加写入到该文件中（否则会直接覆盖掉该文件，要注意），使用如下命令：

`cat ~/.ssh/id_rsa.pub ` 将得到的密钥写入到`authorized_keys`中

`echo x0x0x0x0x0x0x0 >> ~/.ssh/authorized_keys`

保存退出，此时连接服务器只需要`ssh username@ip.address`

**Note：如果服务器不存在`authorized_keys`文件，输入`ssh localhost` 即可解决**

## 配置https证书

```
curl -L https://github.com/acmesh-official/acme.sh/archive/master.tar.gz -O
tar xzf master.tar.gz
cd acme.sh-master
./acme.sh --install --force
source ~/.bashrc
cd ..
rm -r acme.sh-master
rm master.tar.gz
------------------------------------ 使用脚本一键安装
# 设置默认证书
acme.sh --set-default-ca  --server letsencrypt
# 关闭解析代理
在cf中设置仅限dns

~/.acme.sh/acme.sh --issue --debug --dns --yes-I-know-dns-manual-mode-enough-go-ahead-please -d "你的域名.com" -d "*.你的域名.com"

```

证书申请完成后将证书上传到pve

key上传：私钥，证书链上传：fullchain.cer

证书配置完成---撒花。

