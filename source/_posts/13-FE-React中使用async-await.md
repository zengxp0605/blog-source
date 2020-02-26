---
title: React中使用async-await
comments: true
date: 2020-2-26 17:36
categories: [React]
tags: [React]
---

## 解决react-app `regeneratorRuntime is not defined` 报错
- 添加依赖
```sh
yarn add @babel/plugin-transform-runtime --dev
yarn add @babel/runtime
```

- 配置`.babelrc`
```json
"plugins": [
    ["@babel/plugin-transform-runtime",
      {
        "regenerator": true
      }
    ]
  ],
```