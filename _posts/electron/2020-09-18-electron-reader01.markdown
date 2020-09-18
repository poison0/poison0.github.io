---
layout: post
title: Electron开发实战——本地epub阅读器(简介)
category: 技术
keywords: 技术,electron,epub
---
基于electron的web项目-桌面级epub阅读器，代码已在gitHub开源
代码仓库：[Nread](https://github.com/poison0/Nreader)
###技术栈
*   [electron](https://github.com/electron/electron)
*   [Vue](https://github.com/vuejs/vue)
*   [electron-vue](https://github.com/SimulatedGREG/electron-vue)
*   [epubjs](https://github.com/futurepress/epub.js)
*   [ant-design](https://github.com/vueComponent/ant-design-vue)
*   [lowDb](https://github.com/typicode/lowdb)
![开始页面](https://upload-images.jianshu.io/upload_images/18604310-0a9c7287ba9a8362.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![阅读页面](https://upload-images.jianshu.io/upload_images/18604310-a014876defa5b349.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![目录](https://upload-images.jianshu.io/upload_images/18604310-2b1e30deb1a6f437.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


1.安装Electron-vue并初始化（electron版本号为8.4.1）
```
# 安装 vue-cli 和 脚手架样板代码（npm下载慢可以用cnpm）
npm install -g vue-cli
vue init simulatedgreg/electron-vue my-project
#引入ant-design
npm install ant-design-vue --save
```
**注意：**ant-design可能不会使用时可能不会生效，需要在webpack.renderer.config.js里面加入白名单，才能使用
```
let whiteListedModules = ['vue', 'ant-design-vue']
```
2.epubjs用来解析epub书籍的，epubjs使用时可能会报错，由于语法问题，需要修改导出的语法
![](https://upload-images.jianshu.io/upload_images/18604310-a1e71de1fc93823b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


（待续.....）

















