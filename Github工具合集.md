### 一些工具可能是专门为红队设计的，而其他工具则更通用，可以适应红队环境。

警告：此存储库中的材料仅供参考和教育目的。它们不打算用于任何非法活动。

### 什么是ATT&CK框架
>   ATT&CK是一个基于真实观察的数据创建的、全球免费公开可访问的、基于社区驱动发展的，为蓝方和红方提供一种沟通交流的通用语言的对抗行为知识库，整个框架围绕战术和实现战术的具体技术进行描述和举例。

-   信息搜集/侦察-Reconnaissance
    侦察指的是重点关注试图收集信息以计划在未来进行攻击的攻击者，包括主动或被动收集信息以确定攻击目标的技术。以下是在侦察活动中可以使用到的一些工具：

-   crt.sh→httprobe→目击者自动域截图
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#crtsh---httprobe---eyewitness

-   jsendpoints-提取页面中的DOM链接
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#jsendpoints

-   nuclei-漏洞扫描工具
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#nuclei

*   certSniff-证书透明日志关键字嗅探器
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#certsniff

-   gobuster-路径扫描工具
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#gobuster

-   dnsrecon-DNS枚举工具
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#dnsrecon

*   Shodan-网络测绘工具
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#shodanio

*   AORT (All in One Recon Tool)-子域名扫描工具
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#aort

*   spoofcheck-SPF/DMARC记录检查器
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#spoofcheck

*   AWSBucketDump-子域名扫描工具
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#awsbucketdump

-   GitHarvester-GitHub凭据搜索器
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#githarvester

-   truffleHog-GitHub证书扫描器
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#trufflehog

-   Dismap-企业资产扫描工具
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#dismap

-   enum4linux-Windows / samba枚举工具
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#enum4linux

-   skanuvaty——dns/网络/端口扫描器
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#skanuvaty

-   Metabigor-通过API进行开源情报搜集的工具
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#metabigor

-   Gitrob-github敏感信息扫描器
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#gitrob

## 武器化/资源开发-Resource Development
>   资源开发指的是，重点关注试图获取资源以进行攻击的攻击者，包括攻击者为实现目标而获取、购买或破坏/窃取资源的技术。

-   Chimera-Powershell混淆脚本（Bypass）
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#chimera

-   msfvenom-载荷生成工具
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#msfvenom

-   WSH-功能强大的Web Shell生成器和命令行接口工具
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#wsh

-   HTA-HTA Payload生成工具
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#hta

-   VBA-VBA Payload生成工具
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#vba

-   初始访问-Initial Access
    Bash的兔子（USB攻击工具）
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#bash-bunny

-   邪恶的网络钓鱼活动框架
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#evilgophish

-   社会工程人员工具集钓鱼活动框架
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#social-engineer-toolkit-set

-   九头蛇暴力破解工具
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#hydra

-   SquarePhish OAuth/QR code钓鱼框架
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#squarephish

-   King Phisher网络钓鱼活动框架
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#king-phisher

-   执行-Execution
    Responder-针对LLMNR和NBT-NS协议进行投毒的工具
    https://github.com/SpiderLabs/Responder#usage

    git clone https://github.com/SpiderLabs/Responder#usage

​	cd Responder

-   secretsdump-从 Windows 系统中提取密码哈希的工具
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#secretsdump

​	python3 -m pip install impacket

-   evil-winrm-为 Windows 远程管理提供命令行界面的工具
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#evil-winrm

​	Evil-WinRM 允许攻击者使用 WinRM 远程连接到 Windows 机器并执行任意命令。

​	一些功能包括：

​	在内存中加载 Powershell 脚本

​	绕过某些 AV 加载内存 dll 文件

​	加载 x64 负载

​	传递哈希支持

​	上传和下载本地和远程文件

-   Donut -用于在内存中执行 VBScript、JScript、EXE、DLL 文件和 dotNET 程序集的工具。
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#evil-winrm

​	pip3 install donut-shellcode

-   Macro_pack-用于自动混淆和生成 Office 文档、VB 脚本、快捷方式和其他红队格式的工具。
    git clone https://github.com/sevagas/macro_pack.git

​	cd macro_pack

​	pip3 install -r requirements.txt

-   PowerSploit-PowerShell 脚本和模块的集合，可用于实现各种红队目标。
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#powersploit

-   Rubeus-一种可用于执行与 Microsoft Active Directory (AD) 环境相关的各种操作的工具，例如转储密码哈希、创建/删除用户和修改用户属性。
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#rubeus

-   SharpUp -一个很好的工具，用于检查受害者端点是否存在与高完整性进程、组、可劫持路径等相关的漏洞。
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#sharpup

-   SQLRecon-此 C# MS-SQL 工具包专为攻击性侦察和后期开发而设计。
    https://github.com/skahwah/SQLRecon/wiki

-   https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#sqlrecon

​	持久化-Persistence
​	Impacket
​	Impacket为各种网络协议(包括SMB、Kerberos和LDAP)提供了一组低级Python绑定，以及用于与网络服务交互和执行特定任务(如转储密码散列值和创建网络共享)的高级库。

​	它还包括许多命令行工具，可用于执行各种任务，如转储SAM数据库、枚举域信任，以及破解Windows密码。

-   https://github.com/fortra/impacket

-   Empire
    Empire是一个漏洞攻击后的框架，允许你生成有效载荷，以便与受害者系统建立远程连接。

​	一旦有效载荷在受害者系统上执行，它就会建立回帝国服务器的连接，然后可以使用该连接发出命令并控制目标系统。

​	Empire还包含许多内置模块和脚本，可用于执行特定的任务，如转储密码散列值、访问Windows注册表和窃取数据。

​	https://github.com/EmpireProject/Empire



-   SharPersist
    一个用c#编写的Windows持久性工具包。https://github.com/mandiant/SharPersist/wiki https://github.com/mandiant/SharPersist

-   权限提升-Privilege Escalation
    LinPEAS
    LinPEAS是一个很好的、冗长的权限提升工具，用于在Linux端点上查找本地privesc路由。

​	https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS

-   WinPEAS
    WinPEAS是一个很好的、冗长的权限提升工具，用于在Windows端点上查找本地privesc路由。

​	https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS

-   linux-smart-enumeration
    Linux smart enumeration是另一个很好的、不那么冗长的Linux privesc工具。

​	https://github.com/diego-treitos/linux-smart-enumeration



-   Certify
    certification是一个c#工具，用于枚举和滥用Active Directory证书服务(AD CS)中的错误配置。

​	certification旨在与其他红队工具和技术(如Mimikatz和PowerShell)结合使用，使红队能够执行各种类型的攻击，包括中间人攻击、模仿攻击和特权升级攻击。

​	https://github.com/GhostPack/Certify



-   Get-GPPPassword
    Get-GPPPassword是powersplit工具包的一个PowerShell脚本，用于检索使用组策略首选项(GPP)创建和管理的本地帐户的密码。

​	Get-GPPPassword通过在域控制器上的SYSVOL文件夹中搜索包含密码信息的任何GPP文件来工作。一旦找到这些文件，它就解密密码信息并将其显示给用户。

​	https://github.com/PowerShellMafia/PowerSploit/blob/master/Exfiltration/Get-GPPPassword.ps1



-   Sherlock
    PowerShell脚本用于快速查找本地权限升级漏洞缺失的软件补丁。

​	MS10-015:用户模式到振铃(KiTrap0D) —MS10-092:任务调度程序 —MS13-053: NTUserMessageCall Win32k内核池溢出

• MS13-081: TrackPopupMenuEx Win32k NULL Page

• MS14-058: TrackPopupMenu Win32k空指针解引用 —MS15-051: ClientCopyImage Win32k

• MS15-078:字体驱动缓冲区溢出 MS16-016: 'mrxdav。sys的WebDAV

• MS16-032:辅助登录手柄

• MS16-034: Windows内核模式驱动程序EoP

• MS16-135: Win32k特权提升 —CVE-2017-7199: Nessus Agent 6.6.2—6.10.3 Priv Esc

https://github.com/rasta-mouse/Sherlock



-   Watson
    Watson是一个.net工具，旨在枚举缺失的kb，并建议利用特权升级漏洞。

​	非常适合识别缺失的补丁，并建议可以用来利用已知漏洞以获得系统上更高权限的漏洞。

​	https://github.com/rasta-mouse/Watson

-   ImpulsiveDLLHijack
    一个基于c#的工具，可以自动发现和利用目标二进制文件中的DLL劫持。

​	在交战期间，发现的被劫持路径可以被武器化，以逃避EDR。

​	https://github.com/knight0x07/ImpulsiveDLLHijack

-   ADFSDump
    一个c#工具，从AD FS转储各种各样的好东西。

​	https://github.com/mandiant/ADFSDump

-   防御规避-Defense Evasion
    Invoke-Obfuscation
    一个兼容PowerShell v2.0+的PowerShell命令和脚本混淆器。如果受害端点能够执行PowerShell，那么这个工具非常适合创建严重混淆的脚本。

​	https://github.com/danielbohannon/Invoke-Obfuscation

-   Veil
    Veil是一种工具，用于生成绕过常见反病毒解决方案的元ploit有效负载。

​	https://github.com/Veil-Framework/Veil

-   SharpBlock
    一种通过阻止入口点执行来绕过EDR的活动投影DLL的方法。

​	https://github.com/CCob/SharpBlock

-   Alcatraz
    Alcatraz是一个GUI x64二进制混淆器，能够混淆各种不同的pe文件，包括:

​	• .exe

​	• .dll

​	• .sys https://github.com/weak1337/Alcatraz



-   横向移动-Lateral Movement
    crackmapexec
    这是在Windows/Active Directory环境中使用凭据对(用户名:密码，用户名:散列)进行旋转的一个很好的工具。它还提供了其他功能，包括枚举登录用户和爬行SMB共享以执行psexec风格的攻击，使用Powershell自动将Mimikatz/Shellcode/DLL注入内存，转储NTDS。

​	https://github.com/Porchetta-Industries/CrackMapExec

-   WMIOps
    WMIOps是一个powershell脚本，它使用WMI在Windows环境中的本地或远程主机上执行各种操作。

​	https://github.com/FortyNorthSecurity/WMIOps

-   PowerLessShell
    使用MSBuild.exe远程执行PowerShell脚本和命令而不生成PowerShell .exe的工具。

​	https://github.com/Mr-Un1k0d3r/PowerLessShell

-   PsExec
    PsExec是Sysinternals工具套件的一部分，Sysinternals是一组用于管理和排除Windows系统故障的实用程序。

​	它非常适合在目标计算机上远程执行命令。

​	https://learn.microsoft.com/en-us/sysinternals/downloads/psexec

-   LiquidSnake
    Liquid Snake是一款旨在对Windows系统进行横向移动而不接触磁盘的程序。

​	为了在内存中执行. net程序集，该工具依赖于WMI事件订阅，. net程序集将侦听命名管道上的shellcode，然后使用线程劫持shellcode注入的变体来执行它。

​	https://github.com/RiccardoAncarani/LiquidSnake

-   Enabling RDP-Windows RDP enable command
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#enabling-rdp
    Upgrading shell to meterpreter
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#upgrading-shell-to-meterpreter
    Forwarding Ports
    有时，在获得对端点的访问权后，会有本地端口。使这些内部端口可对外路由有助于横向移动到主机上的其他服务。

​	https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#forwarding-ports

-   Jenkins reverse shell
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#jenkins-reverse-shell
    ADFSpoof
    adfspooof有两个主要功能:

    1. 给定来自AD FS配置数据库的EncryptedPFX blob和来自Active Directory的DKM解密密钥，生成用于令牌签名的可用密钥/证书对。


    2. 给定一个签名密钥，生成一个可用于访问联邦应用程序的签名安全令牌。




-   此工具旨在与ADFSDump结合使用。ADFSDump运行在ADFS服务器上，输出您需要使用adfspooof的重要信息。

​	https://github.com/mandiant/ADFSpoof

-   kerbrute
    一个通过Kerberos预身份验证快速强制枚举有效Active Directory帐户的工具。

​	https://github.com/ropnop/kerbrute

-   其他红队工具
    隐藏本地管理员帐号
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#hiding-the-local-admin-account

-   通过删除签名来削弱windows defender
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#cripple-windows-defender-by-deleting-signatures

-   为每个用户启用多个RDP会话
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#enable-multiple-rdp-sessions-per-user

-   Sysinternals PsExec.exe本地替代方案
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#sysinternals-psexecexe-local-alternative

-   靠陆地端口扫描器生活
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#live-off-the-land-port-scanner

-   代理感知的PowerShell下载字符串
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#proxy-aware-powershell-downloadstring

-   在浏览器书签中查找内部端点
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#looking-for-internal-endpoints-in-browser-bookmarks

-   查询DNS记录进行枚举
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#query-dns-records-for-enumeration

-   没有PowerUp的未引用的服务路径
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#unquoted-service-paths-without-powerup

-   使用/k绕过禁用的命令提示符
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#bypass-a-disabled-command-prompt-with-k

-   停止windows防御程序删除mimikatz.exe
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#stop-windows-defender-deleting-mimikatzexe

-   检查您是否在虚拟机中
    https://github.com/A-poc/RedTeam-Tools/blob/main/README.md#check-if-you-are-in-a-virtual-machine