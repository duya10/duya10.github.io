# Git 使用教程


## 什么是 Git？

Git 是分布式版本控制系统

## SVN 和 Git

SVN 是集中式版本控制系统，版本库集中在中央服务器，在个人电脑上从中央服务器 Update 得到最新的版本，在完成工作后，再 commit 到中央服务器。集中式版本控制系统必须联网才能工作，不管是互联网还是局域网。

Git 是分布式版本控制系统，没有中央处理器，个人的电脑就是一个完整的版本库，工作不需要联网。多人各自在自己电脑上对文件进行修改，再将各自的修改推送给他人，这样就可以相互看到别人的修改。

## 安装 Git

下载地址：[Git][Git]

如果要修改安装位置，请放在纯英文路径下，

安装完成后在 Terminal 输入 `git` 出现提示说明安装成功。

点击鼠标右键，选择 `Git Bash here` ，就可以使用 Git 命令行工具，Git Bash 使用的是 MinGW。

## Git 的基本使用

### 配置 Git

本地创建 shh key：
> $ ssh-keygen -t rsa -C "18819457397@163.com" // 邮箱是你在 GitHub 上注册的邮箱

回车之后会要求确认路径和输入密码，使用默认的一路回车即可。

成功之后会在 `C:\Users\Administrator` 下生成 `.ssh` 文件夹，打开文件夹下的 `id_rsa.pub` 文件，复制里面的 key。

回到 Github 主页，进入 `Setting`，左边选择 `SSH and GPG keys`，`New SSH Key`，`Title` 随便填，将刚才复制的 key 粘贴到 `Key` 里，点击 `Add SHH Key` 完成。

![New SSH Key](/images/git_ssh.png)

验证连接是否成功，在 Git bash 下输入：

> $ ssh -T git@github.com

第一次输入会提示是否 `continue`，输入 `yes` 会看到：

![验证连接](/images/git_ssh_cl.png)

表示已经成功连接 github。

设置 username 和 Email，github 每次 commit 都会记录它们。

> $ git config --global user.name "bqwhnn"  
> $ git config --global user.email "18819457397@163.com"  
> > 注意：git config --global 参数，表示你在这台机器上的所有 Git 仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和邮箱。

添加远程地址，将仓库连接到远程服务器

> $ git remote add origin  https://github.com/bqwhnn/bqwhnn.github.io.git  
> // bqwhnn 是 github 的用户名，bqwhnn.github.io 是新建的仓库

进入 `.git`，打开 `config`，可以直接修改内容配置远程地址

![配置远程地址](/images/git_remote.png)

### Git 操作

#### 版本库

> 版本库又名仓库，英文名为 repository，这个仓库（目录）里的所有文件都可以被 Git 管理起来，Git 能追踪每个文件的修改和删除，Git 在任何时刻都能追踪历史，也可以在将来某个时刻将文件“还原”。

创建本地仓库

> $ mkdir XXXX  // 创建文件夹  
> $ pwd         // 显示当前目录  
> $ git init    // 将当前目录变成 git 可以管理的仓库

当前目录下会多出一个 `.git` 的目录，是 Git 来跟踪管理版本的。

创建远端服务器仓库的克隆版本

> $ git clone https://github.com/bqwhnn/bqwhnn.github.io.git

![远端仓库地址](/images/git_url.png)

#### 工作流

本地仓库由 Git 维护的**三棵“树”**组成。*工作目录*：持有实际文件；*暂存区（Index）*：临时保存改动；*HEAD*：指向最后一次提交的结果。

Git 基本工作流程第一步，将改动添加到暂存区：

> $ git add <filename>  
> $ git add *  
> $ git add .  
> $ git status // 提交文件前查看改动状态

第二歩，实际提交改动到 HEAD，还未到远程仓库

> $ git commit -m "代码提交信息"

![三棵“树”](/images/git_workflow.png)

### 推送改动

第三步，将本地仓库的 HEAD 中的改动提交到远端仓库

> $ git push origin master  
> // master 可以改为想要推送的任何分支

如果还没有克隆现有仓库，又想将仓库连接到某个远程服务器并推送改动：

> $ git remote add origin <server>

#### 分支

分支是用来将特性开发绝缘开来的。创建仓库时，**master** 是“默认的”主分支，在其他分支上开发完成后，可以将它们合并到主分支。

![分支](/images/git_branch.png)

> $ git checkout -b XXXXX     // 创建分支  
> $ git checkout master       // 切换回主分支  
> $ git branch -d XXXXX       // 删除分支  
> $ git push origin <branch>  // 将分支推送到远端仓库

#### 更新和合并

> $ git pull [origin master] // 更新本地仓库至最新改动
> > 在你的工作目录中*获取（fetch）*并*合并（merge）*远端的改动
> $ git merge <branch>       // 合并其他分支到当前分支

Git 在尝试自动合并改动时并非每次都成功，可能出现*冲突（conflicts）*。
这时需要修改这些文件来手动合并*冲突（conflicts）*，改完之后执行命令将它们标记为合并成功。

> $ git add <filename>  
> $ git diff <source_branch> <target_branch>  
> > 合并改动之前，可以用 *diff* 命令预览差异

#### 标签

***为软件发布创建标签是推荐的***

> $ git tag 1.0.0 1b2e1d63ff    // 创建名为 1.0.0 的标签，后面是 ID 前几位  
> $ git log     // 获取提交 ID

#### 替换本地改动

> git checkout --<filename>

使用 *HEAD* 中的最新内容替换工作目录文件，已添加到暂存区的改动和新文件不会受到影响。

> $ git fetch origin  
> $ git reset --hard origin/master

丢弃本地的所有改动和提交，到远程服务器获取最新的版本历史，并将本地分支指向它。


## 参考

* [常用 Git 命令清单](http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)
* [Git 工作流程](http://www.ruanyifeng.com/blog/2015/12/git-workflow.html)
* [Github 简明教程](http://www.runoob.com/w3cnote/git-guide.html)
* [git - 简明指南](http://www.runoob.com/manual/git-guide/)

[Git]: https://git-scm.com/

