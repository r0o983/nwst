# Earth -- VlunHub

> 下载靶机:https://www.vulnhub.com/entry/the-planets-earth,755/

* 导入虚拟机并设置好网络
* 开启盒子以及渗透虚拟机-->确保两台主机在同一个网络



## 信息收集:

* 网络信息扫描

  * 端口扫描 -- 已知当前网段为:172.16.148.*

  * > nmap -sS -sV -sC -O -v -T 5 172.16.148.1/24
    >
    > 参数说明: -sS 进行tcp syn扫描
    >
    > -sV 根据服务指纹识别出相应的版本
    >
    > -sC 根据端口识别服务,调用默认脚本
    >
    > -O 检测操作系统
    >
    > -v 显示扫描细节
    >
    > -T 扫描强度(0-5) 级别越高扫描越快

    > 或者使用 netdiscover -i eth0   监听当前网络流量
    >
    > 参数说明: -i 指定网卡进行监听
    >
    > ![image-20220414204745265](/Users/christopher/Documents/GitHub/nwst/earth-vulhub.assets/image-20220414204745265.png)

  * 访问ssh,80以及443端口后均无果

  * 将找到的dns记录映射到本地进行访问

  * > sudo echo "172.16.148.128 earth.local terratest.earth.local" >> /etc/hosts 
    >
    > ![image-20220414205505143](/Users/christopher/Documents/GitHub/nwst/earth-vulhub.assets/image-20220414205505143.png)

  * 尝试输入一些内容,此时得到的结果都是经过加密的,没有相应算法无法进行解密

* 信息收集:目录扫描

  * 使用dirsear进行扫描

    > dirsearch -u earth.local
    >
    > ![image-20220414205805150](/Users/christopher/Documents/GitHub/nwst/earth-vulhub.assets/image-20220414205805150.png)

  * 尝试访问

  * ![image-20220414210005120](/Users/christopher/Documents/GitHub/nwst/earth-vulhub.assets/image-20220414210005120.png)

  * 继续扫描当前网站,查看是否有其他的未知文件或目录

  * > gobuster dir -e -u earth.local -w /usr/share/wordlists/dirb/common.txt
    >
    > 参数说明:
    >
    > dir 扫描目录或文件
    >
    > -e 显示完整路径
    >
    > -u 指定扫描的网址
    >
    > -w 指定字典或字符串位置
    >
    > ![image-20220414212315022](/Users/christopher/Documents/GitHub/nwst/earth-vulhub.assets/image-20220414212315022.png)
    >
    > 不能说是一无所获,只能说是毛都没有

  * 扫描https网址: > https://terratest.earth.local 此处如果未加https则会重定向到earth.local

  * ![image-20220414212931593](/Users/christopher/Documents/GitHub/nwst/earth-vulhub.assets/image-20220414212931593.png)

  * 发现robots.txt文件,打开看看...

  * ![image-20220414213459622](/Users/christopher/Documents/GitHub/nwst/earth-vulhub.assets/image-20220414213459622.png)

  * 此处有一项内容不允许抓取,连续测试文件后缀名,排除已在列表之上的所有扩展名.(直接试txt)

  * 信息收集:查看文件

  * > curl https://terratest.earth.local/testingnotes.txt -k  获取文件---当然也可以在浏览器中打开
    >
    > 参数说明:
    >
    > -k 允许连接到 SSL 站点，而不使用证书
    >
    > ![image-20220414214153186](/Users/christopher/Documents/GitHub/nwst/earth-vulhub.assets/image-20220414214153186.png)

  * 书接上文,此时已经看到当前的加密方式为XOR,且已经得到了用户名,密钥的解密方式可能会存在于`testdata.txt`中尝试将页面中的字符串进行解密

  * ![image-20220414215438804](/Users/christopher/Documents/GitHub/nwst/earth-vulhub.assets/image-20220414215438804.png)

* 使用cyberchef将数据进行解密,首先需要将字符串转换为16进制,在进行xor解密

  * ![image-20220414221146463](/Users/christopher/Documents/GitHub/nwst/earth-vulhub.assets/image-20220414221146463.png)
  * 尝试登陆:
  * ![image-20220414221322145](/Users/christopher/Documents/GitHub/nwst/earth-vulhub.assets/image-20220414221322145.png)
  * 使用shell进行连接----被禁止远程连接. 
  * ![image-20220415112323869](/Users/christopher/Documents/GitHub/nwst/earth-vulhub.assets/image-20220415112323869.png)
  * 此时我们可以通过将反弹shell进行编码来进行绕过,此处使用base64编码,当然使用其他编码也是可以的.
  * ![image-20220415113450920](/Users/christopher/Documents/GitHub/nwst/earth-vulhub.assets/image-20220415113450920.png)
  * 将已准备好的命令进行base64编码后重定向到bash,使用`-e`参数来指定重定向的shell

* 权限提升

  * 当前这个权限肯定是啥也干不了的~
  * 使用查找命令找到当前可执行的二进制文件`find / -perm -u=s 2>/dev/null`
    * 参数说明:
    * find 查找命令
    * -perm mode     文件的权限正好是mode就匹配
      -perm -mode    文件的权限包括mode就匹配（该文件还可以拥有额外的权限属性）
    * -u=s  当前用户可执行的suid的脚本或文件
    * 2为过滤输出,只输出正确的内容,过滤掉错误的信息.
  * ![image-20220415125602811](/Users/christopher/Documents/GitHub/nwst/earth-vulhub.assets/image-20220415125602811.png)
  * 看到一个重置root的文件,执行一下看看
  * ![image-20220415125937905](/Users/christopher/Documents/GitHub/nwst/earth-vulhub.assets/image-20220415125937905.png)
  * 执行失败,将文件下载之后查看内容 `再启一个端口将文件传回`
  * `nc -nvlp 8889 > reset_root` 服务器端监听8889端口并将接受到的文件重命名为reset_root
  * `nv -nc 8889 < /usr/bin/reset_root` 将客户端的文件传回服务器端
  * ![image-20220415130433781](/Users/christopher/Documents/GitHub/nwst/earth-vulhub.assets/image-20220415130433781.png)
  * 在服务器端查看该文件 `使用strings` 来查看该二进制文件
  * ![image-20220415131622450](/Users/christopher/Documents/GitHub/nwst/earth-vulhub.assets/image-20220415131622450.png)
  * 无法得出有效信息,尝试使用工具进行跟踪代码
  * `ltrace ./reset_root` 使用该命令之前需要将代码修改为可执行状态`chmox +x reset_root`
  * ![image-20220415131909783](/Users/christopher/Documents/GitHub/nwst/earth-vulhub.assets/image-20220415131909783.png)
  * 执行到此处后提示缺少文件,根据提示在客户端创建三个文件
  * 再次执行`/usr/bin/reset_root`文件
  * ![image-20220415132511034](/Users/christopher/Documents/GitHub/nwst/earth-vulhub.assets/image-20220415132511034.png)
  * 根据提示运行成功,并将root密码成功重置为`Earth`
  * ![image-20220415132949855](/Users/christopher/Documents/GitHub/nwst/earth-vulhub.assets/image-20220415132949855.png)
  * 以已成功获取到root权限
  * ![image-20220415133120328](/Users/christopher/Documents/GitHub/nwst/earth-vulhub.assets/image-20220415133120328.png)