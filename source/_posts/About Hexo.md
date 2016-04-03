---
title: About Hexo 
---

# 利用node 的hexo 框架在github上搭建博客  
  http://zengxp0605.github.io

## 前提条件
  - 安装node && git
  - 在github建立 user.github.io 仓库

## hexo 的使用
```
$ npm install -g hexo // 全局安装hexo
$ cd /path/to/your/site
$ hexo init // 完成初始化
$ hexo generate  // 生成静态页面
$ hexo server // 本地启动
```

出现下面两条日志 项目已经启动
INFO  Start processing
INFO  Hexo is running at http://localhost:4000/. Press Ctrl+C to stop.

## 安装next 主题
```
$ cd /path/to/your/site
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```

## 更换主题, 进入站点 找到 _config.yml
```
$ theme: next // landscape默认
```

## 配置 _config.yml
```
title: oksite // 修改自己的
subtitle:     // 导航上面的小标题
description: oksite blog  // 描述
author: oksite
language: en
timezone:

url: http://oksite.github.io // 修改自己的 就是域名
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:

theme: next // 主题

deploy:
  type: git
  repository: git@github.com:***/**.github.io.git // 自己的名字
```
ps: deploy的type改成git，然后运行下npm install hexo-deployer-git --save 再hexo g hexo d

## 新建文章 新建单页
```
hexo new"postName" #新建文章
hexo new page"pageName" #新建页面
hexo generate #生成静态页面至public目录
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo help # 查看帮助
hexo version #查看Hexo的版本
以下是简写
hexo g == hexo generate
hexo d == hexo deploy
hexo s == hexo server
hexo n == hexo new
```

## 发布 
```
hexo clean    //清除
hexo generate //生成静态页面
hexo deploy   //发布
```

## 参考
  - http://oksite.github.io/2016/03/30/My-New-Post-1/
