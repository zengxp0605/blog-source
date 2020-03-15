---
title: Git 使用详解
date: 2016-04-02 18:00:00
categories: [Git]
tags: Git
---

### git 小技巧 

#### git 打包发布zip包
```
# git 归档方式，但会包含文件夹下现有文件
git archive -o 123.zip HEAD $(git diff --name-only <历史版本sha1 号>^) 
# 使用管道方式打zip 包
git diff <新版本号-sha1> <历史版本号-sha1> --name-only | xargs zip update.zip
# 使用管道方式打zip 包，不指定版本号，而是直接打当前分支和其他分支的差异文件包(比如：比较远端的master和本分支的差异文件打包上线）
git diff origin/master --name-only | xargs zip package.zip
```



#### 修改最近一次提交说明

```
git commit –amend
```

#### 不产生无用的merge的同步

使用 git 自身功能来回滚代码，取消上一次的修改应该用，但要注意，虽然代码是实现了回滚，同时也会自动产生一条“回滚代码”的 log 。
```
git pull --rebase origin master
```

#### 回滚已上传的更新

```
git revert <sha1_of_commit>
```


#### 回滚master步骤 

```
git reset --hard HEAD~1
git reset --hard HEAD <需要回滚到的位置>
git push origin master  --force<强制回滚>
```


#### git 维护

```
# 查看本地git 的分布情况
git count-objects -v

# .git 目录压缩
git gc --auto
# git 数据一致性分析，可以找到丢失的commit sha1
git fsck

# git 数据清理，包括把丢失的commit 从磁盘上物理删除
git prune

# 建议的命令
git fsck && git gc --prune
 
# 或者可以尝试
git reflog expire --expire=now --all && git gc --aggressive --prune=now
```


#### 格式化log 显示样式

```
# 先设置一个 config 
git config --global alias.lg "log --color --graph --codetty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --"
 
# 使用设置好的别名
git lg
```

#### 其他

* 如果想直接使用 git pull 拉去默认分支,可以在 .git/config 加上
```
[branch "master"]
  remote = origin
  merge = refs/heads/master
  rebase = true
```


#### git 设置全局忽略

```
# 设置全局 忽略列表
git config --global core.excludesfile ~/.gitignore_global

# 本地生成文件

> ~/.gitignore_global
```



------------------------------------------------------------
### git 命令行


#### add 命令

```
# 进入交互式界面
git add -i
```

#### blame 命令

```
# 显示文件修改日志
git blame test1.txt
```

#### branch 命令

```
# 当前所有的branch信息
git branch -avv
# 从主分支master创建branch_0.1分支
git branch branch_0.1 master
```

#### checkout 命令

```
# 更新单个文件
git checkout origin/master -- <文件路径>
# 更新某个文件的某个版本
git checkout origin/master <commit sha1> -- <文件路径>

#切换整个工作区到某个历史版本
git checkout <commit sha1>
git checkout HEAD^ # 回滚内容到上一个版本，效果同 git reset

# 从远程分支更新到本地新建一个分支
git checkout -b <本地新分支名> <远端名称>/<远程分支名>
```



#### config 命令

```
git config --global user.name "yourname"
git config --global user.email your@exam.com
```

#### clone 命令 

```
#仅获取最新版和一个历史版本,即最后2个版本
git clone <git path url> --depth=1
# 检查log 版本
git rev-list master
# 或者
git rev-list master --max-count=10
```

#### commit 命令 

```
# 使用最近commit增补提交，不产生新的commit log
git commit -C HEAD –amend
#  -a是提交所有改动，-m是加入log信息
git commit -a -m "log_message"
```

#### fetch 命令

```
# 获取远程信息
git fetch
# 精简获取部分远程
git fetch --depth=<版本数量>
```

####  log 命令 

```
# 精简查看历史提交记录
git log --oneline -<行数>

#查看某个文件的修改日志
git log --pretty=oneline 文件名

#查看文件的修改内容 (后面的为哈希值)
git show 7957045706302a92453fecb22806072872bb4f97
```

#### diff 命令
```
#查看未提交文件的修改
git diff FILENAME
```


#### stash 命令
> 参考:  [http://www.williamsang.com/archives/2347.html?utm_source=tuicool&utm_medium=referral](http://www.williamsang.com/archives/2347.html?utm_source=tuicool&utm_medium=referral)  
> 这个命令保存add的新文件,或者的文件到缓存区,  commit 后的内容不会保存
```
# 将当前修改存入缓存区(不会被提交)
git stash 
# 查看缓存区列表
git stash list

# 恢复暂存区和工作区进度
git stash pop --index stash@{0}

# 其他
git stash [save message] [-k|--no-keep-index] [--patch]
git stash apply [--index] [<stash>] 不删除已恢复的进度，其他同git stash pop
git stash drop [<stash>] 删除某一个进度，默认删除最新进度
git stash clear 删除所有进度
git stash branch <branchname> <stash> 基于进度创建分支
```

#### merge 命令

```
# 撤销当前merge 中的状态
$ git merge --abort
```

#### push 命令 

```
# 提交本地test分支作为远程的master分支
git push origin test:master
 
# 提交本地test分支作为远程的test分支
git push origin test:test
 
# 如果想删除远程的分支呢？类似于上面，如果:左边的分支为空，那么将删除:右边的远程的分支。
# 刚提交到远程的test将被删除，但是本地还会保存的，不用担心。
$ git push origin :test  --force<此参数表示强制push 可能会对冲掉新的push 结果>    
```


#### svn 命令 

```
# 关联svn
git svn init <url> --no-metadata --no-minimize-url -T <主干名，一般为trunk>
```

#### reset 命令 

```
# 将版本库恢复到HEAD之前的版本
* git reset --hard HEAD~1
* git reset --hard HEAD <版本号>
```


#### remote

```
# 查看远程仓库
git remote -v
# 添加远程仓库
git remote add [name] [url]
#如: get remote add origin https://github.com/zengxp0605/test.git

#删除远程仓库
git remote rm [name]
# 修改远程仓库
git remote set-url –push[name][newUrl]
# 拉取远程仓库
git pull/fetch [remoteName] [localBranchName]
# 推送远程仓库
git push [remoteName] [localBranchName]
# 删除远端已删除，但是本地信息还有分支
git remote prune [origin]
```


#### tags 命令 

```
# 查看本地tags 
git tag -l
 
# push到服务器端 
git push --tags 
 
# 删除本地tag 
git tag -d v1.1
 
# 补打标签
git tag -a v0.1.1 [版本号]
```

#### files 命令 

```
# 恢复多个删除的本地文件
git ls-files -d | xargs -i git checkout {}
```


#### git 保存http下的用户名密码

```
git config --global credential.helper store 
```


### gitconfig 配置

<file txt .gitconfig>
```
[user]
    name = Jason
    email = jason_zeng@admin.com
[core]
    autocrlf = true
    excludesfile = d:/Users/jason/.gitignore_global
    trustctime = false
    editor = vim
    filemode = false
[http]
    # 缓存区大小
    postBuffer = 1048576000
[diff]
    tool = sourcetree
[difftool]
    prompt = false
[difftool "sourcetree "]
    cmd = 'D:/Tools/BeyondCompare/BComp.exe' \"$LOCAL\" \"$REMOTE\"
[merge]
    tool = sourcetree
[mergetool]
    prompt = false
[mergetool "sourcetree"]
    cmd = 'D:/Tools/BeyondCompare/BComp.exe' \"$LOCAL\" \"$REMOTE\" \"$BASE\" \"$MERGED\"
    trustExitCode = true
[alias]
    # 别名
    lg = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --
    co = checkout
    cl = clone
    ci = commit
    st = status -sb
    br = branch
    unstage = reset HEAD --
    dc = diff --cached
[credential]
    # http协议仓库,保持用户名和密码
    helper = store
[push]
    default = matching
[color]
    ui = true
[pack]
    windowMemory = 1G
    SizeLimit = 100m
    threads = 1
    window = 0
    packSizeLimit = 100m
```
</file>


### Sourcetree常用工具和配置方式

BeyondCompare定义为默认工具

  - 点击 工具->选项
  - 在弹出框选择 比较 
  - 外部对比工具 选择Beyond Compare 
  - 合并工具 选择Beyond Compare
  - 找到本机git配置文件 .gitconfig
  - 添加如下配置
  
```
[difftool "sourcetree"]
cmd = '/d/Tools/BeyondCompare/BComp.exe' \"$LOCAL\" \"$REMOTE\"
[mergetool "sourcetree"]
cmd = '/d/Tools/BeyondCompare/BComp.exe' \"$LOCAL\" \"$REMOTE\" \"$BASE\" \"$MERGED\"
trustExitCode = true
```

> 提醒：把 d:/ 改成 Bash 下的 /d/ 


### 详见 [https://github.com/git-tips/tips](https://github.com/git-tips/tips) 
