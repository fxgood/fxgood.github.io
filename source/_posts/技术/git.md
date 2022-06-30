---
title: git使用学习
category: 技术 
excerpt: 记录git的介绍，常用命令和原理。
---

# 版本控制

主流的版本控制器

- Git
- SVN(Subversion)
- CVS
- VSS
- TFS
- Visual Studio Online

分类

- 单机版本控制
- 集中式版本控制：如SVN
  - 版本库是集中放在中央服务器上的，而工作的时候，用的都是自己的电脑，所以首先要从中央服务器获得最新的版本，然后工作。完成工作后，需要把自己做完的活推送到中央服务器。集中式版本控制系统是必须联网才能工作，对网络贷款要求比较高。
- 分布式版本控制：如Git
  - 没有中央服务器，每个人的电脑就是一个完整的版本库，工作的时候不需要联网了，因为版本都在自己的电脑上；
  - 协同的方法：比如自己在电脑上修改了文件a，其他人在电脑上修改了文件a，这时，只需要把各自的修改推送给对方，就可以互相看到对方的修改了。
  - **Git是目前世界上最先进的分布式版本控制系统。**
  - 每个人都有完整代码，有一定的安全隐患，故有些公司会用SVN

# 常用Linux命令

- cd
- cd..
- pwd：当前工作目录
- ls 或 ll：列出当前目录下所有文件ll更详细
- touch：新建文件
- rm：删除文件
- mkdir：新建目录（文件夹）
- rm -r：删除一个文件夹
- mv：移动文件
- reset：重新初始化终端，清屏
- clear：清屏(windows下cls)
- history：查看命令历史
- help：帮助
- exit：退出
- #：注释

# 常用git命令

- `git init`
- `git status` 查看文件夹状态
- `git add .` 当前文件夹所有untracked文件添加到暂存区
- `git commit -m "xxxx"` 将暂存区的内容提交到本地仓库

# Git配置

- `git config -l`  
  - git配置，-l表示清单
- `git config --local -l`
  - local只对某个仓库有效
- `git config --global -l`
  - global对登录用户所有仓库有效（查看当前用户的配置）
- `git config --system`
  - system对系统所有登录的用户有效，基本不用

> 注：优先级 local > global > system，也就是说当对一个仓库同时设置了 global 和 local 那么采用local配置的信息。所有配置文件都保存在本地。

- git相关的配置文件
  - `Git\mingw64\etc\gitconfig`：Git安装目录下的gitconfig --system系统级
  - `C:\Users\Administrator\.gitconf` 只适用于当前登录用户的配置 --global全局
- 两个重要配置命令，用户标识，必要！
  - `git config --global user.name "xxx"`
  - `git config --global user.email "xxx.qq.com"`
  - 这里加了global，对本用户，所有的项目都是用这个用户标识

# git基本理论（核心）

- Git本地有三个工作区域
  - 工作目录 `Working Directory` ：就是平常存放项目代码的地方
  - 暂存区 `Stage/Index`：用于临时存放你的改动，事实上它只是一个文件，保存即将提交的文件列表信息
  - 本地仓库 `Repository`或`Git Directory`：安全存放数据的位置，这里面有你提交的所有版本的数据。其中HEAD指向最新放入仓库的版本
- 远程
  - `Remote Directory`： 远程仓库，托管代码的服务器，可以简单认为是项目组中的一台电脑用于远程数据交换

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220418235248125.png" alt="image-20220418235248125" style="zoom:80%;" align=left />



![image-20220418235927324](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220418235927324.png)

git的一般工作流程

- 在工作目录中添加、修改文件
- 将需要进行版本管理的文件放入暂存区
- 将暂存区的文件提交到git仓库

因此，git管理的文件有三种状态：

- 已修改(modified)
- 已暂存(Staged)
- 已提交(Committed)

![image-20220419000623484](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220419000623484.png)

# git项目搭建

工作目录(Workspace)一般就是你希望git帮助你管理的文件夹，可以是你项目的目录，也可以是一个空目录，建议不要有中文。日常使用下面六个命令即可。

![image-20220419000836812](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220419000836812.png)

## 本地仓库搭建

创建本地仓库的方法有两种

1.创建全新的仓库

```
# 在当前目录新建一个git代码库
$ git init
```



2.克隆远程仓库

```
# 克隆一个项目和它的整个代码历史（版本信息）
$ git clone [url]
```



# git文件操作

文件的四种状态

- `Untracked`
  - 未跟踪，此文件在文件夹中，但是并没有添加到git库，不参与版本控制
  - 通过`git add`状态变为`staged`
- `Unmodify`
  - 文件已入库，未修改，即版本库中的文件快照内容与文件夹中完全一致。
  - 如果它被修改，会变成`modified`
  - 如果使用`git rm`移出版本库，则成为`untracked`文件
- `Modified`
  - 文件已修改，仅仅是修改，并没有进行其他的操作
  - 通过`git add`可进入暂存`staged`状态
  - 使用`git checkout`则丢弃修改过，返回到`unmodify`状态。即从库中取出文件，覆盖当前修改
- `Staged`
  - 暂存状态，执行`git commit`则将修改同步到库中，文件变为`Unmodify`
  - 执行`git resert HEAD filename`取消暂存，文件变为`Modified`

忽略文件

有时候我们不想把某些文件纳入版本控制中，比如数据库文件，临时文件，设计文件等。

在主目录下建立`.gitignore`文件，此文件有如下规则：

1. 忽略文件中的空行以#开始的行会被忽略
2. 可以使用Linux通配符。例如，`*,?,[abc],{string1,string2...}`
3. 如果名称前面有个`!`则表示例外规则，将不被忽略。
4. `/tmp` 忽略 tmp文件夹及其所有内容
5. `tmp/`忽略tmp文件夹下的所有内容（tmp文件夹自身会被版本管理）

```tex
#为注释
*.txt  		#忽略所有以.txt结尾的文件
!lib.txt	#但lib.txt除外
/temp		#仅忽略项目根目录下的TODO文件，但不包括其他目录tmp
build/		#忽略build/目录下的所有文件
doc/*.txt	#忽略 doc/notes.txt 但不包括 doc/server/arch.txt
```

> 注意：如果你创建.gitignore文件之前就push了某一文件，那么即使你在.gitignore文件中写入过滤该文件的规则，该规则也不会起作用，git仍然会对该文件进行版本管理。

# 使用码云

1. 注册码云
2. 设置本机绑定SSH公钥，实现免密码登录

```
# 进入C:\Users\Administrator\.ssh 目录
ssh-keygen # 生成公钥 在~/.ssh/id_rsa.pub

```

3. 将公钥信息public key添加到码云账户中即可
4. 使用码云创建一个自己的仓库

# idea集成git



1. 创建项目
2. 把远程项目直接clone到项目文件夹下（或者从别的文件夹下复制过来）

启用自动add，然后写完以后git commit -m "xxx", git push 即可

# 说明：git分支

![image-20220420000031708](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220420000031708.png)



git分支中常用指令：

```
# 列出所有本地分支
git branch

# 列出所有远程分支
git branch -r

# 新建一个分支，但是依然停留在当前分支
git branch [branch-name]

# 新建分支并且切换过去
git checkout -b [branch]

# 将指定分支合并到当前分支
git merge [branch]

# 删除分支
git branch -d [branch-name]

# 删除远程分支
git push origin --delete [branch-name]
git branch -dr [remote/branch]
```



如果同一个文件在合并分支的时候被修改了则会引起冲突：（协商选择到底要谁的代码）

- 解决的办法是我们可以修改冲突文件后重新提交

master主分支应该非常稳定，用来发布新版本，一般情况下不允许在上面工作，工作一般情况下载新建的dev分支上工作，工作完成后，比如要发布，或者说dev分支代码稳定后可以合并到主分支master上来。
