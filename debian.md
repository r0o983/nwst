# debian 配置教程

## 解决用户不在sudoers中

* 切换至root用户
* vim /etc/sudoers
* 在root ALL=（ALL）ALL 下一行添加
* 用户名 ALL=（ALL）ALL
* 保存退出

## 安装open-ssh

> sudo apt install openssh-server

## 安装telegram

* apt install telegram-desktop

## 安装oh my zsh

* apt install curl git

* `apt install curl git wget zsh && sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`

*  sudo apt-get install fonts-powerline

* 或者
  ```shell
  git clone https://github.com/abertsch/Menlo-for-Powerline.git 
    
  cd Menlo-for-Powerline
  
  sudo mv "Menlo for Powerline.ttf" /usr/share/fonts/ 
  
  sudo fc-cache -vf /usr/share/fonts/
  
  安装提示sudo apt install zsh-syntax-highlighting
  插件安装
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


切换shell

```
  默认shell设置：chsh -s /bin/zsh
  切换回之前的bash：chsh -s /bin/bash
```

## 安装chrome

```bash
  sudo wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
  sudo dpkg -i google-chrome-stable_current_amd64.deb
  sudo apt install -f 
  sudo dpkg -i google-chrome-stable_current_amd64.deb
```

## 安装文泉驿字体

```
sudo apt-get install ttf-wqy-microhei ttf-wqy-zenhei
sudo apt-get install ttf-wqy-zenhei
```

## debian配置开发环境

* java

  ```
  sudo apt-get install default-jdk // 安装默认的jdk版本--默认11
  sudo apt install apt-transport-https ca-certificates wget dirmngr gnupg software-properties-common // 安装第三方库的jdk版本
  wget -qO - https://adoptopenjdk.jfrog.io/adoptopenjdk/api/gpg/key/public | sudo apt-key add -  // 导入密钥
  sudo add-apt-repository --yes https://adoptopenjdk.jfrog.io/adoptopenjdk/deb/ //添加AdoptOpenJDK APT //如果这上一条命令执行不过，
  则使用：echo "deb https://adoptopenjdk.jfrog.io/adoptopenjdk/deb buster main" | sudo tee /etc/apt/sources.list.d/adoptopenjdk.list
  sudo apt update  // 更新源列表
  sudo apt install adoptopenjdk-8-hotspot //安装jdk8版本
  sudo update-alternatives --config java // 设置默认的jdk版本
  sudo apt remove default-jdk // 卸载默认的jdk版本
  sudo apt remove adoptopenjdk-8-hotspot  // 卸载jdk8
  ```

## 安装ftp客户端

  ```text
sudo apt install filezilla filezilla-common -y
  ```

## 安装obs

  ```
sudo apt-get install ffmpeg obs-studio
sudo apt-get install obs-studio
  ```

## 安装多语言

```
sudo apt install locales-all  // 安装多语言

dpkg-reconfigure locales  // 设置多语言环境
```
## 安装桌面监控conky

* 设置开机启动。
    在~/.config/autostart/下面新建一个conky.desktop文件，内容如下：

  ```shell
  [Desktop Entry]
  Type=Application
  Name=Conky
  Comment=Start conky script
  Exec=conky --daemonize --pause=5
  OnlyShowIn=GNOME
  X-GNOME-Autostart-Phase=Application
  Name[en_US]=conky.desktop
  ```

* 找到配置文件（sudo vim ~/.conkyrc   ），并写入以下代码------脚本代码如下：

  ```
  # set to yes if you want Conky to be forked in the background
  background yes
  cpu_avg_samples 2
  net_avg_samples 2
  out_to_console no
  # X font when Xft is disabled, you can pick one with program xfontsel
  #font 7x12
  #font 6x10
  #font 7x13
  #font 8x13
  #font 7x12
  #font *mintsmild.se*
  #font -*-*-*-*-*-*-34-*-*-*-*-*-*-*
  #font -artwiz-snap-normal-r-normal-*-*-100-*-*-p-*-iso8859-1
  # Use Xft?
  use_xft yes
  # Xft font when Xft is enabled
  xftfont Sans:size=11  
  own_window_argb_visual yes
  #own_window_colour hotpink
  # Text alpha when using Xft
  xftalpha 0.8
  # on_bottom yes
  # mail spool
  # mail_spool $MAIL
  # Update interval in seconds
  update_interval 1
  # Create own window instead of using desktop (required in nautilus)
  own_window yes
  own_window_transparent yes
  own_window_hints undecorated,below,sticky,skip_taskbar,skip_pager
  #own_window_type override
  own_window_type desktop
  # Use double buffering (reduces flicker, may not work for everyone)
  double_buffer yes
  # Minimum size of text area
  minimum_size 260 5
  maximum_width 400
  # Draw shades?
  draw_shades no
  # Draw outlines?
  draw_outline no
  # Draw borders around text
  draw_borders no
  # Stippled borders?
  stippled_borders no
  # border margins
  # border_margin 4
  # border width
  border_width 1
  # Default colors and also border colors
  default_color white
  default_shade_color white
  default_outline_color white
  # Text alignment, other possible values are commented
  #alignment top_left
  #minimum_size 10 10
  gap_x 10
  gap_y 35
  alignment top_right
  #alignment bottom_left
  #alignment bottom_right
  # Gap between borders of screen and text
  # Add spaces to keep things from moving about?  This only affects
  # certain objects.
  use_spacer none
  # Subtract file system buffers from used memory?
  no_buffers yes
  # set to yes if you want all text to be in uppercase
  uppercase no
  # none, xmms, bmp, audacious, infopipe (default is none)
  # xmms_player bmp
  
  
  TEXT
  ${color #0277BD}SYSTEM ${hr 1}
  #Time:$alignr${time %Y.%m.%d}$alignc   ${time %H:%M:%S}$alignr   Week:${time %w}
  Hostname: $alignr$nodename
  Kernel: $alignr$kernel
  Machine:$alignr$machine
  Uptime: $alignr$uptime
  Temp: ${alignr}${acpitemp} °C
  Battery:$alignr${battery BAT0}
  #Battery:$alignr${battery_percent BAT0}%  
  ###############
  ${color #006064}NETWORK ${hr 1}
  IP:${addr enp8s0}
  GateWay:$gw_ip
  Down ${downspeed enp8s0} /s ${alignr}Up ${upspeed enp8s0} /s
  ${downspeedgraph enp8s0 25,107} ${alignr}${upspeedgraph enp8s0 25,107}
  Total ${totaldown enp8s0} ${alignr}Total ${totalup enp8s0}
  #IP:${addr wlp8s0}
  #GateWay:$gw_ip
  #Down ${downspeed wlp8s0} /s ${alignr}Up ${upspeed wlp8s0} /s
  #${downspeedgraph wlp8s0 25,107} ${alignr}${upspeedgraph wlp8s0 25,107}
  #Total ${totaldown wlp8s0} ${alignr}Total ${totalup wlp8s0}
  ############
  ${color #26C6DA}MEMORY ${hr 1}
  Ram ${alignr}$mem / $memmax ($memperc%)
  ${membar 4}
  Swap ${alignr}$swap / $swapmax ($swapperc%)
  ${swapbar 4}
  Highest MEM $alignr MEM%
  ${top_mem name 1}$alignr ${top_mem mem 1}
  ${top_mem name 2}$alignr ${top_mem mem 2}
  ${top_mem name 3}$alignr ${top_mem mem 3}
  ${top_mem name 4}$alignr ${top_mem mem 4}
  ${top_mem name 5}$alignr ${top_mem mem 5}
  ##############
  ${color #FF9800}CPU ${hr 1}
  Frequency: ${alignr}${freq dyn} MHz
  Processes: ${alignr}$processes ($running_processes running)
  Load: ${alignr}$loadavg
  CPU1 ${alignr}${cpu cpu1}%
  ${cpubar 4 cpu1}
  CPU2 ${alignr}${cpu cpu2}%
  ${cpubar 4 cpu2}
  CPU3 ${alignr}${cpu cpu3}%
  ${cpubar 4 cpu3}
  CPU4 ${alignr}${cpu cpu4}%
  ${cpubar 4 cpu4}
  Highest CPU $alignr CPU%
  ${top name 1}$alignr${top cpu 1}
  ${top name 2}$alignr${top cpu 2}
  ${top name 3}$alignr${top cpu 3}
  ${top name 4}$alignr${top cpu 4}
  ${top name 5}$alignr${top cpu 5}
  ##############
  ${color #00BFA5}FILE SYSTEM ${hr 1}
  Disk_Read:${alignr}${diskio_read}
  Disk_Write:${alignr}${diskio_write}
  Root: ${alignr}${fs_free /} / ${fs_size /}
  ${fs_bar 4 /}
  Home: ${alignr}${fs_free /home} / ${fs_size /home}
  ${fs_bar 4 /home}
  ##############
  ```

## vim配置文件修改 

* vim /etc/vim/vimrc

```shell
syntax on    #打开语法高亮。自动识别代码，使用多种颜色显示。  
set nu    #显示行号  
hi Comment ctermfg=DarkCyan    #修改默认注释颜色
set hlsearch   #设置高亮搜索  
set cursorline #光标所在的当前行高亮  
set showmatch  #光标遇到圆括号、方括号、大括号时，自动高亮对应的另一个圆括号、方括号和大括号  
```

## debian10 安装docker

  ```shell
sudo apt-get remove -y docker docker-engine docker.io containerd runc // 清理旧版本
sudo apt-get update -y // 更新源
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg2 software-properties-common // 更新https源
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add - // 添加GPG key
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable" // 添加docker源
sudo apt-get update -y // 更新源
sudo apt-get install -y docker-ce docker-ce-cli containerd.io  // 安装docker
docker pull mysql:latest // 拉取最新的mysql镜像
docker images  // 查看当前的镜像
docker run -itd -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 --name mysql-latest mysql  // 运行容器
参数说明：
-i：以交互模式运行，通常配合-t
-t：为容器重新分配一个伪输入终端，通常配合-i
-d：后台运行容器
-p：端口映射，格式为主机端口:容器端口
-e：设置环境变量，这里设置的是root密码
--name：设置容器别名
运行成功后会返回一个容器id


docker ps  // 查看docker运行状态
docker exec -it mysql-latest /bin/bash  // 将容器连接到伪终端中
mysql -u root -p  // 连接mysql

docker restart 容器id  // 启动已存在的容器
  ```

## debian安装显卡驱动

```
deb https://mirrors.ustc.edu.cn/debian/ buster main contrib non-free
deb-src https://mirrors.ustc.edu.cn/debian/ buster main contrib non-free

deb https://mirrors.ustc.edu.cn/debian/ buster-updates main contrib non-free
deb-src https://mirrors.ustc.edu.cn/debian/ buster-updates main contrib non-free

deb https://mirrors.ustc.edu.cn/debian-security/ buster/updates main contrib non-free
deb-src https://mirrors.ustc.edu.cn/debian-security/ buster/updates main contrib non-free
```

> sudo apt install nvidia-settings
>
> nvidia-detect # 查看显卡驱动

官方教程：

> https://wiki.debian.org/NvidiaGraphicsDrivers#Debian_10_.22Buster.22
>
> https://wiki.debian.org/GraphicsCard#nVidia_and_nouveau

1. ```
   deb http://deb.debian.org/debian buster-backports main contrib non-free //添加源
   apt update  //更新系统
   apt install -t buster-backports nvidia-driver firmware-misc-nonfree //安装驱动
   ```

