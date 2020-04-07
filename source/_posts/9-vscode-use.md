---
title: VSCode 使用
date: 2016-07-05 17:00:00
categories: [IDE]
tags: vscode
---

### 智能提示(node,react为例)

1. 安装typings
```
  npm install -g typings 
```

2. 进入项目目录,安装需要提示的类型(这里需要加上 `--global` 参数)  
```
  typings install dt~node --global

  // 支持react
  typings install dt~react --global
  typings install dt~react-native --global
```
<!-- more -->

3. 项目目录下新建 `jsconfig.json` 文件, 内容如下[此文件内容为空也可以??]
```json
  {
    "compilerOptions": {
      "target": "ES6",
      "module": "commonjs",
      "jsx": "react",
      "allowSyntheticDefaultImports": true
    },
    "exclude": [
      "node_modules"
    ]
  }
```

4. 新建`test.js`文件测试
```js
  var fs = require('fs');
  import React from 'react';
  // 输入就能看到补全提示
  React.Component

  fs.readdirSync
```

### 另外的一种方法,通过vscode插件
1. 打开命令模式 `ctrl+shift+p`
2. 安装扩展,输入install
3. 进入插件列表后搜索找到 `Typings Installer`,确认安装
4. 安装后可以通过 `Typings Installer: Typings` 命令找到相对应的模块安装
5. 如: 输入 `node`可以找到 dt~node模块, 全局安装即可
6. 一定要建立 `jsconfig.json` 文件, 方可生效


### 快捷键设置(个人习惯)
```json
[
    {
        "key": "ctrl+shift+down",
        "command": "editor.action.moveLinesDownAction",
        "when": "editorTextFocus"
    },
    {
        "key": "ctrl+shift+up",
        "command": "editor.action.moveLinesUpAction",
        "when": "editorTextFocus"
    },
    {
        "key": "ctrl+shift+d",
        "command": "editor.action.copyLinesDownAction",
        "when": "editorTextFocus"
    },
    {
        "key": "shift+alt+d",
        "command": "workbench.view.debug"
    },
]

```


### 参考
> [https://code.visualstudio.com/docs/languages/javascript](https://code.visualstudio.com/docs/languages/javascript)  
> [http://www.cnblogs.com/IPrograming/archive/2016/04/30/VsCodeTypings.html](http://www.cnblogs.com/IPrograming/archive/2016/04/30/VsCodeTypings.html)  
> [https://github.com/typings/typings](https://github.com/typings/typings)  
