---
title: Git 常用命令
date: 2016-04-02 17:00:00
categories: [Git]
tags: Git
---

> 以Github为例,介绍windows下 Git常用命令的使用

1. 配置 SSH KEYS  (使用 ssh 协议的路径时需要, 使用http/https 无需配置)
   - 在 Git Bash中执行:  ` ssh-keygen -t rsa -C "youremail" `   
   - 此时会在当前用户根目录下生成.ssh文件夹,里面有两个文件：id_rsa , id_rsa.pub  
   - 复制 ~/.ssh/id_rsa.pub 内容, 登陆github账户->Settings->SSH Keys-> add , 粘贴,保存

2. 配置本机全局的用户名和邮箱( 可以设置不同的用户名)  
  - 全局的配置文件: /c/Users/username/.gitconfig
 
 ```
  git config --global user.name "username"
  git config --global user.email "email"

# 设置全局的忽略文件
  git config --global core.excludesfile ~/.gitignore_global
# 设置windows 下使用LF 换行(避免"warning:LF will be replacee by CRLF" 的提示)
git config --global core.autocrlf false
```

3. 基本操作命令
```
  - git clone https://github.com/zengxp0605/test.git jason/test // 克隆一个远程的仓库到本地jason/test目录
  - git status  // 查看当前状态
  - git add filename // 添加单个文件
  - git add * // 或者使用git add -A , 添加所有更新的文件
  - git commit -am "your message" // 提交到本地仓库
  - git push <远程主机名> <本地分支名>:<远程分支名>
  - git push origin master // 推送到远程仓库, 简写: git push (没有默认匹配分支时无法简写)
  - git pull <远程主机名> <远程分支名>:<本地分支名>
  - git pull origin master   // 拉取更新, 简写: git pull 
  - git pull origin next:master  // 取回origin主机的next分支,与本地的master分支合并
  - *.gitignore -> 需要忽略,不需跟踪的文件或目录可以配置到此文件中,支持通配符*
```

4. 本地已有文件的目录需要初始化为git仓库时
```
  - git init  // 初始化仓库
  - git remote add origin git@github.com:facebook/react-native.git  // 添加远程库origin 分支 
  - git remote -v   // 查看远程库,和分支
  - git remote remove origin  // 删除origin 分支
```

5. 关于分支
```
  - git branch  // 查看本地分支, 带星号的为当前分支
  - git branch -a // 查看本地及远程分支
  - git branch testing  // 创建本地分支 testing 
  - git checkout master  // 切换到master 分支
  - git checkout -b new_branch // 本地新建分支
```

6. 关于tags
```
  - git checkout tags/<tag_name> -b <branch_name>  // 拉取远端tag到本地分支
  - git tag v1.0 // 新建标签v1.0
  - git tag -a v1.4 -m 'my version 1.4' // 新建标签并注释
```

#### 各个阶段 查看代码差异：
```
 - git diff  // 已修改,未暂存
 - git diff --cached // 已暂存(add),未提交
 - git diff master origin/master    // 已提交(commit),未推送

```

#### 各个阶段 撤销修改：
```
 - git checkout .  // 已修改,未暂存, 或使用 `git reset --hard`
 - git reset --hard  // 已暂存,未提交, 或者 `git reset && git checkout .`
 - git reset --hard origin/master // 已提交,未推送 (既执行了git add .,又执行了git commit)
 - git reset --hard HEAD^ && git push -f  // 已推送
```

#### 其他常用命令
```
 - git show //显示某次提交的内容
 - git log –p [file] // 查看某个文件每次详细修改的内容
 - git merge origin/master // 当前分支合并主干
```


#### 遇到的一些问题：
1. git push/git pull 时提示需要加入远程和本地仓库地址
  - 方法1: 执行命令 (以下命令同时可以设置为 --global)
    
    ```
    git config branch.master.remote origin
    git config branch.master.merge refs/heads/master
    ```

  - 方法2：修改 ~/.git/config 文件,添加以下行
    
    ```
    [branch "master"]
        remote = origin
        merge = refs/heads/master
    ```

2. 很久未更新的库,其他人有提交代码,自己拉取master时遇到冲突(本地代码并未更改)
- 退出merge状态
`git merge --abort`
- 强制回滚
`git reset --hard HEAD`
- 删除本地多余
`git fetch origin -p`
- 最后拉取代码
`git pull`

3. 拉取代码时冲突,以他人的为准解决
```
# 先还原失败的 git pull
git reset --hard HEAD
# 获取更新
get fetch
# 强制使用他人版本合并
git merge origin/feature/alpha -X thiers
```
