---
layout: post
title: Electron开发实战——本地epub阅读器(电子书的解析与渲染)
keywords: 技术,electron,epub
date: 2020-09-29
Author: AzureWind
tags: [electron]
comments: true
---
解析电子书和epub.js的api的使用

<!-- more -->
epubjs是直接渲染到选择的DOM节点上的，所以如果熟悉api就非常容易就可以解析并显示出来
下面是常用的epubjs的api，如果需要了解更多可以跳转到[官网](https://github.com/futurepress/epub.js) 查询

1. 渲染电子书   

```javascript
//引入epubjs
import Epub from 'epubjs'

// 生成book
this.book = new Epub(path)

 // 生成rendition read 是渲染的dom节点的id
this.rendition = this.book.renderTo('read', {
    width: "100%",
})

// 显示电子书
this.rendition.display()

//获取主题对象
this.themes = this.rendition.themes

//电子书生成之后的回调
this.book.ready
.then(() => {
})
.then(result => {
})

```
2. 电子书的常用操作   

```javascript
//下一页
nextPage() {
    if (this.rendition) {
        this.rendition.next()
        this.syncIndex()
    }
}
//上一页
prevPage() {
    if (this.rendition) {
        this.rendition.prev()
        this.syncIndex()
    }
}
 // 目录跳转
jumpTo(href) {
    this.rendition.display(href).then(res => {
        this.closeAllTable()
        this.syncIndex()
    })
}
// 切换字号
setFontSize(size) {
    if (this.themes) {
        this.themes.fontSize(size + 'px')
    }
},
// 改变字体
this.themes.font("微软雅黑")
```

