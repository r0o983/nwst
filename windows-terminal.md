

# windows terminal 安装与配置（憨批式教程）

## 安装terminal

微软商店搜索terminal或者

> https://github.com/microsoft/terminal

## 配置scoop

建议：使用管理员账户开启powershell

> ```
> set-executionpolicy remotesigned -scope currentuser   //允许本地脚本执行
> ```

`iex (new-object net.webclient).downloadstring('https://get.scoop.sh')`

执行命令安装scoop

`scoop help` 

检测是否安装成功

scoop基础使用方式为：scoop 动作--- 对象

例：scoop install git  安装git

scoop search git	搜索git

scoop update	更新软件

scoop uninstall git 卸载

## 配置terminal

### 设置配色

在`schemes`中定义配色方案，使用以下格式定义

```
{
    "background" : "#282A36",
    "black" : "#21222C",
    "blue" : "#BD93F9",
    "brightBlack" : "#6272A4",
    "brightBlue" : "#D6ACFF",
    "brightCyan" : "#A4FFFF",
    "brightGreen" : "#69FF94",
    "brightPurple" : "#FF92DF",
    "brightRed" : "#FF6E6E",
    "brightWhite" : "#FFFFFF",
    "brightYellow" : "#FFFFA5",
    "cyan" : "#8BE9FD",
    "foreground" : "#F8F8F2",
    "green" : "#50FA7B",
    "name" : "Dracula",
    "purple" : "#FF79C6",
    "red" : "#FF5555",
    "white" : "#F8F8F2",
    "yellow" : "#F1FA8C"
}
```

默认填写位置位于：`"schemes": []` 方括号内填写以上内容

### 配置主题

```
        {
             "acrylicOpacity" : 0.70, //亚克力背景透明度（需启用useAcrylic）
            "background" : "#012456", //背景颜色，PS默认为蓝色
            "closeOnExit" : true, //关闭窗口的时候退出所有挂载的程序
            "colorScheme" : "Dracula", //配色方案（Dracula需导入）
            "commandline" : "powershell.exe", //此处终端打开PS
            "cursorColor" : "#FFFFFF", //光标颜色
            "cursorShape" : "bar", //光标形状（默认为bar，即条状）
            "fontFace" : "MesloLGM Nerd Font",
            "fontSize" : 12,
            "guid" : "{61c54bbd-c2c6-5271-96e7-009a87ff44bf}", //唯一标识符，随机生成
            "historySize" : 9001, //缓存大小
            "icon" : "ms-appx:///ProfileIcons/{61c54bbd-c2c6-5271-96e7-009a87ff44bf}.png", //图标
            "name" : "PowerShell", //在下拉菜单里显示的名称
            "tabTitle" : "PowerShell", //在选项卡上显示的名称
            "padding" : "0, 0, 0, 0", //内容的边框距，默认填充全部空间
            "snapOnInput" : true, //输入的时候自动滚动到输入位置
            "startingDirectory" : "%USERPROFILE%", //初始工作目录，默认为用户目录
            "useAcrylic" : true //使用亚克力效果
        },
```

将以上内容写入`profiles`下的`defaults`中，**注意**此处的`colorScheme`需要跟上面配色的进行一致，如果你有其他的配色就自行安排

## 安装powershell组件

依次执行以下命令,安装必须的两个组件

 `Install-Module posh-git -Scope CurrentUser `

`Install-Module oh-my-posh -Scope CurrentUser `

**如果尚未安装 NuGet，可能需要安装它。 如果是这种情况，PowerShell 命令行会询问是否要安装 NuGet。 选择 [Y]“是”。 你可能还需要批准从不受信任的存储库 PSGallery 中安装模块。 选择 [Y]“是”。**

### 配置powershell的配置文件

在`PowerShell`中输入`notepad $profile`用文本打开powershell的配置文件，并输入以下内容

```shell
Import-Module posh-git
Import-Module oh-my-posh
Set-PoshPrompt -Theme Paradox  //此处与官网文档给出的安装不同，md这是个神坑
#推荐主题
Set-PoshPrompt -Theme agnoster
Set-PoshPrompt -Theme Argonaut
```

## 字体设置

https://github.com/ryanoasis/nerd-fonts/blob/master/patched-fonts/Meslo/M/Regular/complete/Meslo%20LG%20M%20Regular%20Nerd%20Font%20Complete.ttf

直接下载安装即可！

