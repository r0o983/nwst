# git学习

## 安装git及初始化

> 下载git直接安装即可

### 配置git用户名以及邮箱等信息

> git config --global user.name "x0x0"
>
> git config --global user.email "xxx@gmail.com"
>
> git config --global color.ui true 配置颜色信息

### 初始化本地仓库

> git init 

### 添加文件到当前仓库

> git add **.txt

### 创建版本控制

> git commit -m "first"

参数-m: 添加说明

### 查看当前文件修改了那些数据

> git diff xxx.txt

## 版本回朔

### 查看当前git日志

> git log  // 查看当前日志
>
> git log --pretty=oneline  //只输出版本号（16位）

### 返回上一个版本,或某一个版本

> git reset head~ //回退一个版本
>
> git reset head~10 //回退10个版本

### 返回某个特定的版本

> git reset --head HEAD~ //回退上一个版本
>
> git reset --hard 7975c8f  //返回一个已经回退的版本

### 命令回朔

> git reflog  //找到你之前操作的命令

### 小结

现在总结一下：

- `HEAD`指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令`git reset --hard commit_id`。
- 穿梭前，用`git log`可以查看提交历史，以便确定要回退到哪个版本。
- 要重返未来，用`git reflog`查看命令历史，以便确定要回到未来的哪个版本。

## 管理修改

### 工作区和暂存区

> 每次修改的文件都会存在工作区，需要使用git add命令来添加到暂存区

### 提交文件到仓库

> 每次修改文件都需要使用git add 来将文件保存到暂存区，使用git commit -m "**" 来提交到当前分支

### 文件区别

> git diff HEAD --readme.txt  //查看当前文档和服务器上文档的区别

### 撤销修改

> git checkout -- filename 

命令`git checkout -- readme.txt`意思就是，把`readme.txt`文件在工作区的修改全部撤销，这里有两种情况：

一种是`readme.txt`自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；

一种是`readme.txt`已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

总之，就是让这个文件回到最近一次`git commit`或`git add`时的状态。

注：只能撤销一次

`git checkout -- file`命令中的`--`很重要，没有`--`，就变成了“切换到另一个分支”的命令，我们在后面的分支管理中会再次遇到`git checkout`命令。

**在没有提交之前可以使用** > git reset HEAD file 把暂存区的文件给退回到本地工作区,之后再使用checkout来进行回退

### 删除文件

> rm test.txt  //删除已提交的本地文件或仓库文件
>
> git status //查看当前状态。如果确定要删除则使用> git rm test.txt 并且commit
>
> 以上命令会直接在当前版本库删除文件

**撤销删除**

> git checkout -- test.txt
>
>  注意：从来没有被添加到版本库就被删除的文件，是无法恢复的！

#### 小结

命令`git rm`用于删除一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失**最近一次提交后你修改的内容**。

## 远程仓库

**以github举例：**

1. 先在本地生成密钥

   ` ssh-keygen -t rsa -C "email.com"`一路下一步即可，生成的密钥位置在主目录的.ssh目录下

2. 添加到github中

   `打开github找到setting，ssh and gpg keys设置，`将生成的公钥内容复制到ssh中即可。

### 添加远程库

在github上创建一个公开仓库，选择从本地上传文件到远程仓库

`git remote add origin git@github.com:christopher-x/nwst.git`

执行上面命令之前,需要先初始化本地库`git init `

`git branch -M main`

`git push -u origin main`

### 克隆远程仓库

登陆帐号，找到需要克隆的远程仓库，使用如下命令即可

`git clone git@github.com:christopher-x/TCPNotes.git`

## 分支管理

### 创建分支与合并管理

当前版本存在主分支用于提交，`master`分支`Head`严格来说不是指向提交，而是指向`master`，`master`才是指向提交的，所以，`head`指向就是当前分支

1. 创建分支

   `git chechout -b dev`  创建分支并切换到当前分支

   `git checkout`命令加上`-b`参数表示创建并切换，相当于以下两条命令：

   `git branch dev ` 创建分支

   `git checkout dev ` 切换分支

2. 查看当前分支

   `git branch`

   当前分支前会出现一个`*` 号
   
3. 在当前分支创建一个文件并合并到主分支

   ```
   git checkout dev //切换到dev分支
   echo hello,this is my first projects > README.md
   git add . //将新增的文件添加到当前分支本地暂存区  ps：这一步可省略
   git commit -m "upgrade"  ps：这一步可省略
   git checkout main //切换回主分支
   git merge dev //将dev分支的工作成果合并到主分支，此时在dev的文件内容就和主分支的文件一样了
   ```

4. 删除分支

   `git branch -d dev` 

   删除dev分支，删除之前需切换到其他分支

5. switch

   `git switch -c dev`

   创建并切换到新的dev分支

   `git switch main`

   切换到已有的分支

   使用新的`git switch`命令，比`git checkout`要更容易理解。

#### 小结

Git鼓励大量使用分支：

查看分支：`git branch`

创建分支：`git branch `

切换分支：`git checkout `或者`git switch `

创建+切换分支：`git checkout -b `或者`git switch -c `

合并某分支到当前分支：`git merge `

删除分支：`git branch -d `

### 分支管理策略

使用`--no-ff`来进行提交合并，参数为：

`git merge --no-ff -m "merge with no-ff"` dev 

因为本次合并需要创建一个新的commit，所以需要加上-m参数，把commit的描述写进去

此时，就可以用`git log`来查看分支历史

合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并。

### bug分支

当出现bug时需要切换到其他临时分支时，可以使用`stash`来保存当前分支状态

`git stash` 保存当前分支状态

切换到其他分支完成工作后返回当前分支，使用`git status` 看到工作区是干净的

使用如下命令来查看当前保存的工作现场

`git stash list` 查看保存的工作区

恢复工作区

`git stash apply stash@\{0\}` 恢复当前工作区，stash内容并不会删除，需要使用`git stash drop`来删除

`git stash pop` 恢复工作区的同时会将stash内容一并删除

其他工作区如果也存在同样的bug则可以使用`cherry-pick`命令来进行复制一个特定的提交到当前分支

`git branch ***` 切换分支

`git chrry-pick 0x00e1` 在当前分支重新提交已修复的bug，已修复已知bug

#### 小结

修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；

当手头工作没有完成时，先把工作现场`git stash`一下，然后去修复bug，修复后，再`git stash pop`，回到工作现场；

在master分支上修复的bug，想要合并到当前dev分支，可以用`git cherry-pick `命令，把bug提交的修改“复制”到当前分支，避免重复劳动。

### 多人协作

`git remote -v ` 显示远程仓库的详细信息

`git push origin *name` 推送某一个分支

从本地推送分支，使用`git push origin branch-name`，如果推送失败，先用`git pull`抓取远程的新提交

- 在本地创建和远程分支对应的分支，使用`git checkout -b branch-name origin/branch-name`，本地和远程分支的名称最好一致
- 建立本地分支和远程分支的关联，使用`git branch --set-upstream branch-name origin/branch-name`
- 从远程抓取分支，使用`git pull`，如果有冲突，要先处理冲突

## 标签管理

**给当前commit的文件弄一个我们能记住的名字**

### 创建标签

`git branch` 切换到需要打标签的分支上

`git tag v1.0` 当前分支的标签为1.0

`git tag` 查看所有标签

给已经存在的commit打上标签，找到之前的commit id

`git tag v0.9 f52c66`

`git show <tagname>` 查看当前版本的标签信息

创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字：

`git tag -a v0.1 -m "version 0.1 released" 1094adb`

### 操作标签

如果想要删除某个本地标签可以使用

`git tag -d v0.1`

#### 操作标签到远程仓库

`git push origin v1.0` 推送1.0的标签到远程仓库

`git push origin --tags` 推送所有本地标签到远程仓库

删除远程仓库的标签--需要先从本地删除

`git tag -d v0.9` 

然后从远程仓库删除 

`git push origin :refs/tags/v0.9`

- 命令`git push origin `可以推送一个本地标签；
- 命令`git push origin --tags`可以推送全部未推送过的本地标签；
- 命令`git tag -d `可以删除一个本地标签；
- 命令`git push origin :refs/tags/`可以删除一个远程标签

## 自定义操作

### 忽略某些特定的后缀名文件

在Git工作区的根目录下创建一个特殊的`.gitignore`文件，然后把要忽略的文件名填进去，Git就会自动忽略这些文件。

忽略文件的原则是：

1. 忽略操作系统自动生成的文件，比如缩略图等；
2. 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的`.class`文件；
3. 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。

强行提交某些**已忽略**文件到git远程仓库

`git add -f app.class` 使用-f参数来强行添加到git

使用`git check-ignore`来检查规则是否正确

```
git check-ignore -v app.class
.gitignore:3:*.class  app.class
```

排除当前文件的忽略情况

```
# 不排除.gitignore和App.class:
!.gitignore
!App.class
```

- 忽略某些文件时，需要编写`.gitignore`；
- `.gitignore`文件本身要放到版本库里，并且可以对`.gitignore`做版本管理！

### 配置别名

`git config --global alias.st status` 将status简化为st

`--global`参数是全局参数，也就是这些命令在这台电脑的所有Git仓库下都有用。

`git config --global alias.last 'log -1'` 显示最后一次的提交信息

`git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"` 提交结构改良

#### 配置文件

配置Git的时候，加上`--global`是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用。

配置文件放哪了？每个仓库的Git配置文件都放在`.git/config`文件中：

当前用户的Git配置文件放在用户主目录下的一个隐藏文件`.gitconfig`中：

```
[user]
        name = x0x0
        email = xxxx@gmail.com
[color]
        ui = true
[alias]
        st = status
        last = log -1
        lg = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit

```

