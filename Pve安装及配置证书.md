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

**普通ISO文件目录为`/var/lib/vz/template/iso`**

## 开启远程桌面

``sudo apt install xrdp `` 安装后该服务会默认进行启动，使用``systemctl status xrdp``查看状态

使用windows默认远程工具即可连接

## 安装配置oh-my-zsh

`apt install curl git wget zsh && sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`

```shell
cd ~/.oh-my-zsh/plugins
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting


# 首先用vim进入.zshrc配置文件
vim ~/.zshrc

# 之后利用vim编辑文件为
plugins=(
		git
        zsh-syntax-highlighting
        zsh-autosuggestions
        )

# 刷新配置文件
source ~/.zshrc
```



## 安装frp并设置开机自启动

`https://github.com/fatedier/frp/releases` 下载适合自己系统的版本

服务器端使用frps进行启动，客户端使用frpc。

简易启动`./frps -c ./frps.ini ` 

配置参数.`frps.ini ` 

### **服务器端**

```shell
[common]
bind_port = 7000 ## 服务器端默认端口
vhost_http_port = 80 ## http端口
vhost_https_port = 443  ## https端口
authentication_method = token  ## 使用token进行认证
token = XXXXXXXXXXXXX ## 越复杂越好,客户端需要与服务器端一致


##配置wab端进行可视化访问
dashboard_addr = 0.0.0.0
dashboard_port = 7200
dashboard_user = admin	
dashboard_pwd = admin
#dashboard_tls_mode = true
#frp logs

## 添加日志
log_file = ./frps.log ## 日志存放路径
log_level = info  ## 日志级别
log_max_days = 30 ## 日志保留天数

## 如下的证书没用到---准确来说是没配好
tls_cert_file = /etc/frp/server.crt
tls_key_file = /etc/frp/server.key

```

由于下载的包内不存在`.service`文件,所以这里必须要重新创建一个文件来进行配合

创建一个名为:`frps.service`的文件,写入如下内容

```shell
[Service]
Type = simple
ExecStart = /usr/bin/frps -c /etc/frp/frps.ini


[Install]
WantedBy = multi-user.target
```

将`frps.service` 存放到`/lib/systemd/system/`下

将`frps` 移动到 `/usr/bin/`中

创建一个新的文件夹`mkdir /etc/frp/` ，将`frps.ini` 移动到该目录下

设置开机自启动

`systemctl enable frps.service`

### 客户端

简易启动`./frpc -c ./frpc.ini ` 

```shell
[common]
server_addr = 23.55.21.162 # 服务器IP
server_port = 7000 # 服务器端口
authentication_method = token  # 认证方式
token = XXXXXXXXXXXXX # 保持与服务器端一致即可
log_file = ./frpc.log # 日志存放路径
log_level = info # 日志级别 一共5个级别
log_max_days = 30 # 日志保留天数

[ssh] # 标题
type = tcp # 使用协议
local_ip = 127.0.0.1 # 需要映射的IP
local_port = 22 # 本地端口
remote_port = 6000 # 远程端口(访问服务器端口


[web] # 标题
type = https # 使用协议
local_ip = 192.168.11.2 # 本地IP
local_port = 8006 # 本地端口
custom_domains = abc.xxx.com,www.abc.xxx.com ## 需要映射的域名,如果有多个则需要使用逗号分隔

[web1]
type = https
local_ip = 192.168.11.3
local_port = 8006
custom_domains = aaa.bbb.com,www.aaa.bbb.com

## 使用https2http 一直没成功,不知道咋回事... 有大佬有了解的欢迎指导...(
#plugin = https2http
#plugin_local_addr = 192.168.0.3:8006
#plugin_crt_path = /etc/frp/server.pem
#plugin_key_path = /etc/frp/server.key
#plugin_host_header_rewrite = 127.0.0.1
#plugin_header_X-From-Where = frp
```



配置frpc.service文件

```shell
[Service]
Type = simple
ExecStart = /usr/bin/frpc -c /etc/frp/frpc.ini


[Install]
WantedBy = multi-user.target
```

将frpc.service 存放到`/lib/systemd/system/`下

将`frpc 移动到 `/usr/bin/`中

创建一个新的文件夹`mkdir /etc/frp/` ，将`frpc.ini` 移动到该目录下

设置开机自启动

`systemctl enable frpc.service`



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

## ~~配置https证书~~(好像不能用了)

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

~~证书申请完成后将证书上传到pve~~

~~key上传：私钥，证书链上传：fullchain.cer~~

~~证书配置完成---撒花。~~

~~/.acme.sh/acme.sh --issue --debug --dns --yes-I-know-dns-manual-mode-enough-go-ahead-please -d "freeworklab.com" -d "*.freeworklab.com"~~



~~qm importdisk 100  /var/lib/vz/template/iso/bleach-plus-20210904-openwrt-x86-64-generic-squashfs-combined-efi.img local-lvm~~

## 使用cloudflare进行配置

1. 注册账户
   - 账户名(随便填)
   - <img src="https://raw.githubusercontent.com/r0o983/images/main/image-20221016174937203.png" alt="image-20221016174937203" style="zoom: 33%;" />
   - 邮件(随便填一个,暂时还没发现有什么用处)
2. 添加插件
   - 插件ID:(随便写)
   - DNS API(选择:Cloudflare Managed DNS)
   - 只需要填写如下两项
   - <img src="https://raw.githubusercontent.com/r0o983/images/main/image-20221016175304525.png" alt="image-20221016175304525" style="zoom: 50%;" />
   - CF_Account_ID,找到cloudflare--点击域名---选择账户ID进行复制即可
   - <img src="https://raw.githubusercontent.com/r0o983/images/main/image-20221016175606265.png" alt="image-20221016175606265" style="zoom:50%;" />
   - CF_Token--点击获取您的API令牌---创建令牌
   - <img src="https://raw.githubusercontent.com/r0o983/images/main/image-20221016175725401.png" alt="image-20221016175725401" style="zoom:50%;" />
   - <img src="https://raw.githubusercontent.com/r0o983/images/main/image-20221016175935397.png" alt="image-20221016175935397" style="zoom:50%;" />
   - 点击继续--》创建令牌. 将获取的token填入CF_Token即可
3. 点击PVE
   - 点击凭证
   - 使用账户(选择刚才申请的ACME的账户)
   - 添加域名(xxxx.xxxx.com)
   - 点击立即预定凭证
   - 等待完成!!!

























## PVE 网络配置

`vi /etc/network/interfaces` 修改能访问到的IP地址

`vi /etc/resolv.conf 设置dns地址.  刚进入pve的时候没网

`vi /etc/issue` 修改控制台地址

`vi /etc/hosts.  修改控制台地址

## Pve 硬盘挂载

1. 新硬盘格式化

安装一个新硬盘后，

在终端输入，查看硬盘分区：

ls -al /dev/sd*

当然也可以进入web管理页面，节点 --> 磁盘。

就能看到你新接入的设备名称

使用fdisk 命令给新硬盘分一个区

```undefined
fdisk /dev/sdb
```

​	硬盘格式化为ext4

```undefined
ls -al /dev/sd*
```

sdb1 就是分出来的新分区，但还没有格式化

```undefined
mkfs.ext4 /dev/sdb1 -m0
```

### 挂载硬盘

新增一个文件夹，名称可以自定义

```kotlin
mkdir /mnt/pve/data
```

将sdb1挂载到 /mnt/pve/data

```kotlin
mount /dev/sdb1 /mnt/pve/data
```

### 启动自动挂载

```csharp
ls -alF /dev/disk/by-uuid/
```

记下 sdb1 的那一长串 uuid，我这里是 xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

将uuid写入 /etc/fstab 中，以后每次开机就会自动加载新硬盘，并挂载到 /mnt/pve/data 目录下。

```undefined
vim /etc/fstab
```

加入一行

```kotlin
UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  /mnt/pve/data  ext4  defaults 0 1
```

按esc，输入:wq ，回车保存退出。

查看是否挂载成功

```undefined
mount -a
```

让系统将/etc/fstab的所有内容重新加载，这样不用重启。

```undefined
mount -l
```

### 添加新的存储

pve界面中进入，数据中心 --- 存储 --- 目录，点击添加即可





## PVE 安装CT后无法启动

使用如下命令即可解决

```shell

# 修改grub文件
vim /etc/default/grub

#修改GRUB_CMDLINE_LINUX_DEFAULT="quiet" 为以下命令
GRUB_CMDLINE_LINUX_DEFAULT="systemd.unified_cgroup_hierarchy=0 quiet"

# 更新grub
update-grub

# 重启
reboot
```



