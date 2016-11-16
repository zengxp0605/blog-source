---
title: 使用Electron制作简单的桌面应用
comments: true
date: 2016-07-07 22:33:57
categories: [nodejs]
tags:
keywords: Electron
---

### 安装环境(首先安装nodejs)

```
npm install --g electron-prebuilt

```

##### 安装的时候需要翻墙, 如果修改部分源代码

- 这里说一下修改源

- 先执行上面的安装命令, 执行到 " Downloading electron-v1.2.6-win32-x64.zip" 的时候,如果卡住了

- 找到全局安装目录下的 `electron-prebuilt\node_modules\electron-download` 目录下的 `index.js`

将下面一段url地址改成镜像源
```
  var url = process.env.NPM_CONFIG_ELECTRON_MIRROR ||
    process.env.ELECTRON_MIRROR ||
    opts.mirror ||
    'https://github.com/electron/electron/releases/download/v'
  url += process.env.ELECTRON_CUSTOM_DIR || opts.customDir || version
  url += '/'
  url += process.env.ELECTRON_CUSTOM_FILENAME || opts.customFilename || filename

```

改为

```
 // 根据自己的版本做修改
  var url = 'https://npm.taobao.org/mirrors/electron/1.2.6/electron-v1.2.6-win32-ia32.zip';
```

然后重新执行 `npm install --g electron-prebuilt`


#### 其他依赖, 为了方便这里统一全局安装

```
// 打包使用, 可能同样需要修改`electron-download`模块index.js中的 url
npm install -g electron-package

// 加密用
npm install -g asar

```
electron-packager的打包基本命令是：

```         
electron-packager <location of project> <name of project> <platform> <architecture> <electron version> <optional options>

```

在package.json中添加脚本
```json
  "scripts" : {
    "start": "electron .",
    "packager": "electron-packager ./app appName --all --out ./OutApp --version 1.2.6 --overwrite --icon=./app/img/icon.ico"
  }

```

然后可以通过下面的命令来打包, 打完包以后 会在./OutApp下生成对应平台的包
```
npm run packager
```

加密源代码
```
asar pack ./app app.asar
```

### 实例demo
目录结构
```
appName
    |- OutApp
    |- README.md
    |- package.json
    |- node_modules
    |- app
        |- index.html
        |- main.js
        |- package.json
        |- renderer.js

```

运行前先 `npm install`

代码参考 github:[electron/electron-quick-start]( https://github.com/electron/electron-quick-start) 


#### 参考
> [https://segmentfault.com/a/1190000004843033?_ea=710087](https://segmentfault.com/a/1190000004843033?_ea=710087)  
> [https://segmentfault.com/a/1190000004863646](https://segmentfault.com/a/1190000004863646)  
> [https://github.com/electron/electron-quick-start]( https://github.com/electron/electron-quick-start)  


