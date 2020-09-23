---
layout: post
title: Electron开发实战——本地epub阅读器(引入lowDb)
keywords: 技术,electron,epub
date: 2020-09-22
Author: AzureWind
tags: [electron]
comments: true
---
存储用户数据库使用了lowDb，是一个基于Node的JSON文件数据库。
之所以选用lowDb，是因为它足够轻量，api又足够丰富，虽然对于处理大文件性能有点捉襟见肘，
但是对于只存储书籍信息的阅读器来说，可以说是非常合适的数据库了。
<!-- more -->

1. 在项目根目录安装
```
    npm install --save-dev lowdb
```
2. 引入lowDb
```javascript
    const low = require('lowdb')
    const FileSync = require('lowdb/adapters/FileSync')
    import LodashId from 'lodash-id'
    const adapter = new FileSync('db.json')
    const db = low(adapter)
    db._.mixin(LodashId)
    
    export default db
```
3. 书籍信息的增删改查

```javascript
    import db from "./datastore";
    
    //更新或添加数据
    export function setMeta(meta, url, path) {
        if (!db.has('posts').value()) {
            db.defaults({books: []})
                .write()
        }
        if (!db.get('books')
            .find({id: meta.identifier})
            .value()) {
            db.get('books')
                .push({id: meta.identifier, title: meta.title, creator: meta.creator, url: url, path: path, schedule: 0})
                .write()
        }
    }
    
    //删除书籍数据
    export function deleteMeta(id) {
        db.get('books').remove({id: id}).write()
    }
    
    //是否存在书籍数据
    export function isMeta(id) {
        return !!db.get('books')
            .find({id: id})
            .value();
    }
    
    //获取所有数据数据
    export function getAllMeta() {
        return db.read().get('books').value()
    }
    
    //创建书籍数据
    export function createBooks() {
        db.defaults({ books: [] })
            .write()
    }
    
    //获取书籍信息
    export function getMeta(id) {
        return db.get('books')
            .find({id: id})
            .value()
    }
```
4. 用户信息的读取和插入

```javascript
    //设置进度
    export function setSchedule(id,schedule) {
        db.get('books')
            .find({id: id})
            .assign({schedule: schedule})
            .write()
    }
    
    //更新或添加设置
    export function setSetting(info) {
        if (!db.has('setting').value()) {
            db.defaults({setting: {bgColor: 1, fontSize: 16, fontFamily: 1}})
                .write()
        }
        //更新背景色
        if (info.bgColor) {
            db.set('setting.bgColor', info.bgColor)
                .write()
        }
        //更新字体大小
        if (info.fontSize) {
            db.set('setting.fontSize', info.fontSize)
                .write()
        }
        //更新字体
        if (info.fontFamily) {
            db.set('setting.fontFamily', info.fontFamily)
                .write()
        }
    }
    
    //获取设置项
    export function getSetting() {
        return db.read().get('setting').value()
    }
```
5. 最终的db.json 代码如下  
    ***书籍设置项***
    - id 书籍的id
    - title 书籍信息
    - creator 作者
    - url 封面base64数据（由于太大没有显示完整）
    - path 书籍保存的地址
    - schedule 阅读进度   
    ***用户设置项***
    - bgColor 背景颜色
    - fontSize 字体大小
    - fontFamily 字体类型
```json
{
  "books": [
    {
      "id": "4219785412",
      "title": "浮生六记",
      "creator": "（清）沈复著；朱奇志　校译",
      "url": "data:image/jpeg;base64,", 
      "path": "./books/4219785412.epub",
      "schedule": 0
    }
  ],
  "setting": {
    "bgColor": 3,
    "fontSize": 24,
    "fontFamily": 3
  }
}
```
