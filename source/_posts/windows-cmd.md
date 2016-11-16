---
title: windows下常用cmd命令
comments: true
date: 2016-03-15 22:33:57
categories: [windows]
tags: [windows]
keywords:
---

## windows 下杀死进程
```
    netstat -aon  // 查看端口情况
    netstat -aon | findstr "8080"  // 查看对应端口的pid

    tasklist | findstr "7612" // 查看pid=7612 的进程名称

    taskkill /im /f node.exe   // 强制停止 node.exe 进程
```

## 一些常用命令
```
     nslookup google.com.hk 8.8.8.8 //查询 google.com.hk 的ip,( 8.8.8.8 是DNS 服务器)

     // 列出文件目录树形结构
     tree 
     tree /f
     // 导出到文件
     tree /f > d:/tree.txt

```











  Usage
  -----
  
  ### Key Bindings
  
  The default key bindings:
  
  **Windows, Linux:**
  
  * <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>O</kbd>: Preview Markup in Browser.
  * <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>X</kbd>: Export Markup as HTML.
  * <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>C</kbd>: Copy Markup as HTML.
  
  **OSX:**
  
  * <kbd>⌘</kbd>+<kbd>⌥</kbd>+<kbd>O</kbd>: Preview Markup in Browser.
  * <kbd>⌘</kbd>+<kbd>⌥</kbd>+<kbd>X</kbd>: Export Markup as HTML.
  * <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>C</kbd>: Copy Markup as HTML.