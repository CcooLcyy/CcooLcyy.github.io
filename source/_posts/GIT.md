---
title: Git
date: 2021-06-27 23:58:32
tags: tools
---

开源、分布式

git可以追踪纯文本的更改，对于二进制文件或者图像视频，只能知道进行了更改，但是更改了什么内容无法得知。git可以追踪.doc文件，但是无法得知文件更改了什么内容，因为.doc文件是二进制的，git无法得知修改了什么内容。

# 配置

关于整个系统、用户、项目配置文件的所在地以及关系

有三种配置关系，层级从高到底分别是系统级别->用户级别->仓库级别

- 系统级别

   对系统中所有用户都可用的配置文件`/etc/gitconfig`

   ```shell
   git config --system
   ```

- 用户级别

   对特定用户可用的配置文件在 `~/.gitconfig`该用户的所有仓库都有效
   ```shell
   git config --global
   ```

- 仓库级别

   当前项目的配置文件目录`.git/config`必须进入某个git仓库，仅仅针对该项目有效
   ```shell
   git config --local
   ```

需要注意的是配置回从低到高进行覆盖

local＞global＞system

## 用户信息

```shell
## 配置到当前用户个人的用户名和电子邮件地址
git config --global user.name "Daniel"
git config --global user.email Cheng_mmm@iCloud.com
```

同时，需要注意的是，使用了参数`--global`会更改用户个人的配置文件，而不是全局的配置文件。同时，如果去掉参数即可。

## 配置代理

有时候需要访问GitHub，可以将流量通过代理。

```shell
git config --global http.proxy 'http://127.0.0.1:7890'
git config --global https.proxy 'http://127.0.0.1:7890'
```

具体的端口号还是需要根据小猫咪的来看。

## 配置信息的查看

```shell
$ git config --list
user.email=Cheng_mmm@iCloud.com
user.name=CcooLcyy
credential.helper=store
```

如果拥有重复的变量名则他们是属于不同的配置文件。
同时也可以只查看某个特定的环境变量

~~~shell
git config user.name
CcooLcyy
~~~

# 基本概念

有工作区、暂存区（index、stage）、版本库

在工作区的文件可以理解为还没有加入暂存区进行追踪，使用`git add <file>`加入暂存区进行跟踪。同时`git add`可以同时添加多个文件。

## 工作区

在本地电脑中能看到的目录就是工作区

## 暂存区和版本库

在工作区之下有一个`.git`文件夹，这个是git的版本库。暂存区也在`.git`里面被成为stage，同时也有git自动创建的master分支。object是git的对象库，位于`.git/object`下，包含了创建的对象以及文件。我们在工作区进行修改，通过`git add`将文件添加到暂存区，之后使用`git commit`将暂存区的文件保存到master分支中

```shell
## 将暂存区的目录树写入到版本库中，
git commit

## 将HEAD的目录树覆盖到暂存区，也就是清空暂存区
git reset HEAD

## 清空暂存区，并且将已添加到暂存区的文件删除
git rm --cache <file>

## 将工作区和暂存区的文件替换为HEAD中的文件，清空暂存区和工作区
git checkout HEAD .
git checkout HEAD <file>
```

# 操作

## 掌握状态

```shell
## 随时掌握工作区的状态
git status
## 查看工作区和暂存区的不同
git diff
```

通过上面的命令可以知道那些文件还没有加入git也就是还放在工作区，以及加入暂存区之后但是没有提交到版本库的文件。

## 创建仓库添加文件

将一个目录作为git仓库

```shell
## 将当前目录作为git仓库
git init
```

执行之后生成一个`.git`目录，所有的git所需的资源都在这个目录之下。

```shell
## 如果当前目录下已经有几个文件想进行版本追踪
git add <file1>
git add <file2>
```

```shell
## 将暂存区文件同步到版本库，不加文件名会将所有暂存区文件都添加到版本库git commit -m 'file descripation'
```

> 在Linux中commit信息使用单引号
>
> 在Windows系统中使用双引号

## 克隆项目

```shell
## 从现有仓库中拷贝项目到指定目录git clone <repo> <directory>## 同时，在当前目录clone的时候后面跟上一个新的文件夹名字可以直接克隆到文件夹
```

## 提交和修改

```shell
## 添加文件到暂存区git add## 将暂存提交到本地仓库git commit
```

### 删除

删除也放在修改当中说，是因为，删除也是一种修改。

将一个文件添加到版本库之后，在工作区删除。检查git状态

```shell
$ git status On branch masterChanges not staged for commit:  (use "git add/rm <file>..." to update what will be committed)  (use "git restore <file>..." to discard changes in working directory)	deleted:    testing.txtno changes added to commit (use "git add" and/or "git commit -a")
```

系统会提示我们工作区中没有文件但是在版本库中有，这个时候我们有两个操作

1. 这个文件确实对我们没有用了

   ```shell
   ## 直接删除版本库中的文件git rm <filename>
   ```

2. 删错了，但是我们可以直接从版本库中恢复。

   ```shell
   git checkout -- <filename>
   ```

   `git checkout`就是将版本库中的版本替换工作区中的版本，可以直接还原。

#### 丢弃工作区的改动

```shell
## 当我们想丢弃工作区的改动时git checkout -- <filename>
```

1. 文件自修改以后就没有提交到暂存区撤销之后和原版本库一模一样
2. 提交到暂存区之后又做了修改，将撤销修改到暂存区的状态。

其实也就是用暂存区的状态来替换工作区，如果暂存区和版本库统一状态则同步到版本库，如果提交到暂存区但没同步到版本库则撤销到版本库状态。

#### 丢弃暂存区的改动

```shell
## 将暂存区撤销回工作区git reset HEAD <filename>
```

使用reset既可以回退版本库也可以将暂存区会撤销到工作区状态。

而HEAD则表示最新版本。

#### 丢弃版本库的改动

这个就是单纯的回滚版本库，只要不提交到远程都不晚。

### 日志以及版本回退

版本的滚动只是将指向版本的指针进行了移动，因此版本滚动非常快。

通过`git log`我们可以知道commit的记录

```shell
## 查看历史提交记录git log## 以比较简短的方式显示版本信息git log --pretty=oneline
```

版本回退我们可以将我们的文件倒回倒之前已经保存的位置。

~~~shell
## 进行版本回退git reset --head HEAD^## HEAD表示当前版本## 一个^表示上一个版本，两个就上两个版本。## 100个可以写HEAD~100
~~~

版本前滚我们可以找到我们要滚动的版本号直接输入版本号就行，当然也得能找得到目标版本号。

---

如果关闭了终端版本号找不到了也能通过命令找得到

```shell
## 查看每一步的版本号git reflog
```

可以显示出每一次提交的版本号。通过这些版本号就可以直接前滚和回滚版本

# 分支管理

HEAD指向当前分支。当在主分支上时，主分支才是当前提交。

换句话说如果当前有一个dev分支，并且切换到dev分支上，那么HEAD指向的就是dev分支。此时提交是dev分支。

关于分支git是使用指针，因此创建和移动分支非常快。

我们当前在dev上进行工作，完成之后要将dev合并到master上，就是git直接把master指向dev就完成了合并

删除分支同样，就是吧dev分支的指针删除了

## 创建分支

```shell
## 创建dev分支并切换到dev分支上。git checkout -b dev## 上一条命令等同于git branch devgit checkout dev
```

#### 合并分支

```shell
## 将分支切换到master并且将dev分支合并目标分支git branch mastergit merge dev
```

合并之后可以选择直接删除分支

```shell
git branch -d dev
```

#### 解决冲突

在合并的时候如果其他分支和master分支有冲突需要先解决冲突，不然无法合并分支。

```shell
## 查看分支合并图git log --graph
```

#### 禁用快进模式

通常git在合并的时候使用的是快进模式，这种模式在删除分支后分支信息便丢失。

```shell
## 禁用快进模式git merge --no-ff -m '禁用快进模式' dev
```

应为我们会为本次合并添加一次提交，因此要使用参数`-m`来记录描述。

在合并时最好禁用快进合并，这样我们就可以检查出曾经做过哪些合并。

#### 分支合并模式

`Fast-forward`：快进模式，直接将master指向dev的当前提交，合并速度很快。

#### 分支管理思路

master：只用来发布新版本，应该非常稳定才行。

dev：是不稳定的，其他人应该在dev分支上干活。

​	个人分支向dev分支上进行合并，调试稳定之后合并到master上进行版本发布。

bug：用来修复本地的bug，可以不推送分支。

feature：放飞自我的地方。

#### 修复bug（暂存区存储）

情景：工作时有一个急需修复的bug，但是暂存区中的文件还没有提交，但是现在有无法提交，工作没有完成。这个时候可以先吧暂存区存储起来，修复完bug之后再恢复现场继续工作。

```shell
## 当暂存区还有文件没提交的时候git stash## 查看stash的工作现场git stash list
```

解决bug之后我们可以将stash中的工作现场pop出来，也可以apply出来，但是需要drop删除

```shell
## 从stash恢复git stash apply stash@{0}## 从stash删除git stash drop stash@{0}## 上述两条命令可以直接写为git stash pop
```

#### feature 分支

当我们开发新功能的时候，最好新建一个分支，我么无法知道这个新功能能否落地，我们不希望吧dev甚至是master搞乱，因此我们最好新建一个feature分支，以防万一我们提交了之后项目取消了，这样会很麻烦。

所以我们要解决的问题是，在不合并分支的情况下删除原有分支。

```shell
# 正常删除分支，但是会显示分支没有合并，无法删除。
git branch -d feature
# 使用命令强制删除
git branch -D feature
```

这样我们就可以在不合并分支的情况下，强行删除我们不需要的分支。

## 远程仓库

可以使用GitHub作为远程仓库，但是GitHub上的仓库是公开的，也就是任何人都可以看得到。想仅自己可见以来可以交钱设置私人仓库，再一个就是搭建个人呢的git服务器。

# GitHub

一些常规的使用方法

## 通过SSH访问

在我们用户目录下查看有没有`.ssh`目录，如果没有

```shell
# 将电子邮件地址设置成自己的电子邮件地址
# 之后一路回车默认ssh-keygen -t rsa -C 'Cheng_mmm@iCloud.com'
```

> 关于ssh的用法，以后会单独详细的学习，这里不展开说明。

在`.ssh`目录下会存在`id_rsa` `id_rsa.pub`两个文件，前一个是私钥不能泄露，后一个文件是公钥

我们登录GitHub的用户设置界面将我们的公钥内容复制到ssh里面。

## 添加远程仓库

将本地仓库关联到远程仓库。

```shell
# 进行简单替换，将本地仓库和远程仓库关联
# git remote add origin git@github.com:<xxxx>/<reponame>.git
```

我们要保证远程仓库的列表中有ssh公钥。

远程库的名字是origin，这是git的默认叫法，一般来说，origin就是远程库，当然也可以改名字。

## 推送到远程仓库

```shell
## 第一次推送仓库并且关联仓库git push -u origin master
```

```shell
## 如果要推送其他分支git push origin <branchname>
```



因为是第一次推送我们加上参数`-u`，这样会把本地的master推送的远程的master分支，并且还会把远程的master与本地的分支关联起来。

## 从远程库克隆

```shell
## 这个很好懂git clone git@github.com:<xxxxx>/<reponame.git>
```

## 多人协作解决远程仓库冲突

```shell
## 查看远程仓库信息git remote -v
```

但同时如果没有绑定远程库，或者没有权限就看不到地址。

在另一台电脑的其他分支比如dev开发的时候，需要先创建远程的dev分支到本地，

```shell
git checkout -b dev origin/dev
```

这个时候就可以在另一台电脑上开发dev分支了。

同时问题也出现了，如果在另一台电脑上开发了一段时间之后，并且push到了远程仓库，而且原先电脑也在进行开发，那么就意味着远程库与原先电脑有了冲突。这个时候我们要pull远程库到原先电脑的时候就会报错。

解决步骤

1. 尝试将远程仓库pull下来，如果失败，可能是没有建立远程仓库和本地仓库dev的链接

   ```shell
   ## 制定远程分支和本地分支的链接## 提示 no tracking informationgit branch --set-upstream dev origin/dev
   ```

2. 尝试pull，并且git会提示有冲突，需要手动解决。解决方法和本地解决冲突的方法是一致的。

3. 解决冲突之后提交版本库

   ```shell
   git push origin <branchname>
   ```

# 标签

标签就是对版本库做的标注。一个标签只属于一个版本库，也就想是版本库的快照。但他本质上还是指针。同时他不能想指向commit的指针那样移动，他是不可动的。真因为是操作指针，他的创建和删除速度很快。

```shell
## 在分支上打标签首先要切换到目标分支git branchgit checkout <branchname>
```

```shell
## 打标签git tag <tagname>## 查看所有标签git tag
```

如果有一个历史标签该打但是没有打，那么我们可以通过`git log`查找到`commit id`打上标签。

```shell
## 查找历史commitID的方法git log --pretty=oneline --abbrev-commit## 打标签git tag <tagname> <commitid>
```

要注意的是标签并不是按照时间顺序来打的而是使用字母排序

参数`-a`制定标签名`-m`制定说明文字

```shell
## 查看标签信息git show <tagname>
```

本地标签删除

```shell
## 如果本地标签打错了可以直接删除git tag -d <tagname>
```

而推送本地标签到远程库也是比较方便，跟推送版本库的所做大相径庭

```shell
## 推送单个标签到远程git push origin <tagname>## 成批推送标签到远程git push origin --tags
```

既然标签可以推送到远程那一定可以送远程删除，但是可能稍微麻烦一点。

首先要将本地的标签删除，之后推送到远程库

```shell
git tag -d <tagname>git push origin :refs/tags/<tagname>
```

## 自定义git

### 更改ui颜色

```shell
## 显示不同的颜色，让ui更醒目git config --global color.ui true
```

### 忽略部分文件

在某些文件不适合提交的时候可以将某些文件写入`.gitignore`文件中，这样git就会自动忽略这些文件。

同时`.gitignore`文件也要放进版本库中

1. 自动生成或者由编译生成的中间文件和可执行文件等。
2. 带有敏感信息的文件。

## 搭建git服务器

1. 安装git

2. 创建一个git用户

3. 收集需要提交用户的公钥

   导入到`/home/git/.ssh/authorized_keys`一行一个

4. 创建一个没有工作区的git仓库，只用来共享代码

   ```shell
   git init --bare <reponame>.git
   ```

5. 修改仓库权限

   ```shell
   sudo chown -R git:git <reponame>.git
   ```

6. 禁用git的shell登录

   `/etc/passwd`

   ```shell
   git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
   ```

   这样每当使用shell登录的时候都会自动退出。
