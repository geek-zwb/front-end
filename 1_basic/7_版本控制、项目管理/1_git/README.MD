# git 版本控制

## Table of Contents
1. [git 参考开发流程](https://github.com/geek-zwb/front-end/tree/master/1_basic/7_%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6%E3%80%81%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/1_git#git-参考开发流程)
1. [git 常用命令](https://github.com/geek-zwb/front-end/tree/master/1_basic/7_%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6%E3%80%81%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/1_git#git-常用命令)
1. [搭建 git 服务器](https://github.com/geek-zwb/front-end/tree/master/1_basic/7_%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6%E3%80%81%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/1_git#搭建-git-服务器)

## git 参考开发流程
1. 从master分支创建develop分支；

1. 每次开发时，从develop分支拉出feature分支，feature分支命名规则为feature/*****；

1. 在feature分支上开发完成后，开发自己保证自己开发的功能完备、正确；

1. 将feature分支合并到develop分支，CI从develop分支上自动拉下代码持续集成；

1. 从develop分支拉出release分支，交付给测试，release分支命名规则为release/sprint***；

1. 测试在release分支上打tag并完成测试，tag命名规则如RTL-MOFA-0.14.0.8；

1. 测试完成后，将release分支交付给运维；

1. 运维在release分支上打tag，tag命名规则如RTM-MOFA-0.14.0.8，并通知项目负责人将release分支合并到master分支上。

1. 当线上有bug需要修复时，从部署的tag上，拉出batch分支，命名规则为patch/bug****，经测试后，合并到develop分支，如果遵循以上原则，patch会最终会和入master分支。

1. 注意： 一些为了特定目的，比如特定客户poc而开发的版本，不要按以上流程。而是单独拉出独立分支，独立分支的命名规则如branch/lewa_poc

**这里提一下第4步，有一个新手容易犯的错误。将feature代码合并到develop前，需要将最新的develop的代码合并过来。如下：**
```
# 切换到develop
git checkout develop
# 拉取最新的develop代码
git pull
# 切回feature/test
git checkout feature/test
# 合并
git merge develop
# 解决冲突(如果有的话)， commit
# 切换到develop
git checkout develop
# 合并
git merge release/test
# 提交最新代码
git push
```

## git 常用命令
### 初始化
```sh
$ git config --list     # 显示当前Git配置

# 设置提交代码时的用户信息
$ git config [--global] user.name"[name]"
$ git config [--global] user.email"[email address]"

$ git init      # 在当前目录新建一个Git代码库

# 下载一个项目和它的整个代码版本（但不包含分支，需手动新建分支关联）
$ git clone[url]

#与远程仓库关联，也可以直接clone后自动关联
$ git remote add origin [url]
```

### 增删
```sh
# 添加指定文件到仓库暂存区，可以指定多个文件用空格隔开
$ git add [file1] [file2] ...
# 例子
$ git add name1.txt name2.txt name3.txt

# 添加指定目录到暂存区，包括子目录
$ git add [dir]

# 添加当前目录的所有文件到暂存区
$ git add .

# 删除工作区文件，并且将这次删除放入暂存区
$ git rm [file1] [file2] ...

# 停止追踪指定文件，但该文件会保留在工作区
$ git rm --cached [file]

# 改名文件，并且将这个改名放入暂存区
$ git mv [file-original] [file-renamed]# 例子
$ git mv oldName.txt newName.txt
```

### 显示
```sh
# 查看git本地仓库当前状态
$ git status

# 查看暂存区文件与仓库文件之间的不同修改
$ git diff

# 查看git详细日志，包含不同版本提交、时间、作者（前面配置email和name这里会有用）
$ git log
# 查看简洁化日志 只显示commit信息
$ git log--pretty=oneline

# 查看命令记录，回退版本时可根据命令编号来
$ git reflog

# 显示暂存区和最新commit的差异
$ git diff --cached [file]

# 显示工作区与当前分支最新commit之间的差异
$ git diff HEAD

# 显示某次提交的元数据和内容变化
$ git show [commit]
```

### 提交
```sh
# 提交暂存区到本地仓库区
$ git commit -m [message]

# 提交暂存区的指定文件到本地仓库区
$ git commit [file1][file2] ... -m [message]

# 提交工作区自上次commit之后的变化，直接提交到本地仓库区，只针对修改后的文件
# 若出现新的文件，还是需要使用add命令，而不是用这个合成后的命令
$ git commit -am [message]

# 如果上一次的commit没有push到远程仓库中，使用如下代码可以修改提交描述
# 前提是没有修改文件，否则是一次新的添加效果
$ git commit --amend -m [message]
#----------------------------------
#通过vim编辑器来修改提交描述，效果一样
$ git commit --amend
```

### 查看分支
```
# 查看所有分支
$ git branch -a

# 查看本地分支
$ git branch

# 列出所有远程仓库分支
$ git branch -r
```

### 创建分支
```
# 新建一个分支，但依然停留在当前分支
$ git branch branchName

# or 本地从当前所在分支上创建一个新分支
$ git checkout -b branchName

# or 拉取远程某个分支到本地
git checkout -b 本地分支名 origin/远程分支名
```

### 切换到分支
```
$ git checkout branchName
```

### 删除本地分支
```
$ git branch -d branchName # 删除分支

$ git branch -D branchName # 强制删除
```

### 删除远程分支
```sh
$ git push origin --delete [branch-name]

$ git branch -dr [remote/branch]
```

### 查看本地和远程分支 `-a`
```
$ git branch -a
```

### 合并分支
```
# 切换到所需要的branch
$ git checkout master
# 合并分支
$ git merge branchName
```

### 重命名分支
```sh
$ git branch -m | -M oldbranch newbranch
```

### 暂存工作状态
```
$ git stash # 隐藏当前工作区，存入git栈

$ git stash list # stash 列表

$ git stash pop # apply last and remove it from the list

$ git stash apply stash@{1} # 取出指定版本号

$ git show stash@{0} # see the last

$ git stash clear # 清空
```

### reset
```sh
# 重置暂存区与工作区，与上一次commit保持一致
$ git reset --hard

# 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致
$ git reset --hard [commit]
```

### tag
```sh
# 打个 tag
$ git tag -a "tagName" -m "remark"
```

## 搭建 git 服务器
### 服务器
> 假设远程服务器ip为`109.202.107.52`， 端口号`22`

- 新增用户
```
adduser git
// adduser 会给新增用户在home下自动创建目录， useradd 则不会
```

- 设置密码
```
passwd git
```

- 修改`git`登陆方式
```
vi /etc/passwd

git:x:1084:1084::/home/git:/bin/bash
改为
git:x:1084:1084::/home/git:/usr/bin/git-shell
```

- 创建`authorized_keys`
```
cd /home/git/
mkdir .ssh
source authorized_keys
```

- 将客户端的公钥即`id_rsa.pub`内容复制到`authorized_keys`. 当有多个客户端时，追加即可。

**一个客户端一行，追加时起一个新行，中间不要留空行！！！**

- 所属用户与组
```
chown -R git:git /home/git/.ssh
```

- 权限
```
chmod 700 /home/git/.ssh
chomd 600 /home/git/.ssh/authorized_keys
```

- 初始化`git`仓库
```
mkdir /opt/git
# /opt/git 你随意
cd /opt/git
git init --bare test.git
# test.git 你随意
```

- 属主与组
```
chown -R git:git test.git
```

### 客户端
- 对于本地已有库
```
cd g:/xampp/htdocs/test
git remote reset-url ssh://git@109.202.107.52:22/opt/git/test.git
git add .
git commit -m "解释"
git push -u origin master:master
```

- 没有
```
cd g:/xampp/htdocs
git clone ssh://git@109.202.107.52:22/opt/git/test.git
```

> 注意上边的链接格式 `ssh://git@109.202.107.52:22/opt/git/test.git`

> 网上教程都是 `git clone git@yinnote.com:22/opt/git/test.git`
> 怎么弄的？存疑（ip 绑定域名？）

## 参考
- 公司 git 开发流程
