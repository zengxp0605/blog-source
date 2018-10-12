<<<<<<< HEAD
---
title: Win系统下Nodejs NPM相关配置整理
comments: true
date: 2018-4-20 13:46
categories: [nodejs]
tags: [nodejs]
keywords: nodejs npm config
---

## Windows 系统下设置Nodejs NPM全局路径
```
# 查看配置命令
npm config ls -l

# 设置缓存路径
npm config set cache "C:\Program Files\nodejs\node_cache"

# 设置全局安装模块路径
npm config set prefix "C:\Program Files\nodejs"

# 淘宝源镜像
npm set registry https://registry.npm.taobao.org 

```

## 其他
```
# 查看包的历史版本 (版本太多列不出来??)
npm view webpack versions 

# 显示所有安装的模块(限制层级)
npm list --depth=0

# 查看全局安装过的包
npm list -g --depth=0

# 清除缓存
npm cache clean --force

```

=======
---
title: Win系统下Nodejs NPM相关配置整理
comments: true
date: 2018-4-20 13:46
categories: [nodejs]
tags: [nodejs]
keywords: nodejs npm config
---

## Windows 系统下设置Nodejs NPM全局路径
```
# 查看配置命令
npm config ls -l

# 设置缓存路径
npm config set cache "C:\Program Files\nodejs\node_cache"

# 设置全局安装模块路径
npm config set prefix "C:\Program Files\nodejs"

# 淘宝源镜像
npm set registry https://registry.npm.taobao.org 

```

## 其他
```
# 查看包的历史版本 (版本太多列不出来??)
npm view webpack versions 

# 显示所有安装的模块(限制层级)
npm list --depth=0

# 查看全局安装过的包
npm list -g --depth=0

# 清除缓存
npm cache clean --force

```

>>>>>>> db6b3d19cc5377565824e65452db02d91fce437b
