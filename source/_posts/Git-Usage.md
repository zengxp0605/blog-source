---
title: Git 常用命令
date: 2016-04-02 17:00:00
categories: [Git]
tags: Git
---

> 以Github为例,介绍windows下 Git常用命令的使用

1. 配置 SSH KEYS  (使用 ssh 协议的路径时需要, 使用http/https 无需配置)
   - 在 Git Bash中执行:  ` ssh-keygen -t rsa -C "youremail" `   
   - 此时会在当前用户根目录下生成.ssh文件夹，里面有两个文件：id_rsa , id_rsa.pub  
   - 复制 ~/.ssh/id_rsa.pub 内容, 登陆github账户->Settings->SSH Keys-> add , 粘贴,保存

2. 配置本机全局的用户名和邮箱( 可以设置不同的用户名)  
  - 全局的配置文件: /c/Users/username/.gitconfig
 
 ```
  git config --global user.name "username"
  git config --global user.email "email"
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
  - git pull origin next:master  // 取回origin主机的next分支，与本地的master分支合并
  - *.gitignore -> 需要忽略，不需跟踪的文件或目录可以配置到此文件中,支持通配符*
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
  - git branch  // 查看本地分支， 带星号的为当前分支
  - git branch -a // 查看本地及远程分支
  - git branch testing  // 创建本地分支 testing 
  - git checkout master  // 切换到master 分支
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
