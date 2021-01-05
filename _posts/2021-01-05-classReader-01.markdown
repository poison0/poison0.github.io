---
layout: post
title: class结构分析器（简介）
category: 技术
date: 2021-01-05
Author: AzureWind
tags: [java,electron]
comments: true
---
NClassReader是一个图形化的class文件分析工具，功能和javap类似，参考了Java Class Viewer,使用前端编写，利用electron封装成应用。
写这个的原因主要是因为要熟悉class文件结构。  
1 遵循java8jvm规范解析，java8以后的暂时没有添加，以后可能会加上。  
2 博客内容只包含如何解析class结构，不包含electron和相关依赖的使用方式，只包含逻辑代码。
3 本文参考了《Java虚拟机规范（Java SE 8版）》《深入理解Java虚拟机（第3版）》

<!-- more -->

### 使用electron的 dialog 模块选择文件，并用node的fs模块读取文件二进制
extensions表示扩展名，title是弹出框的title  
选中之后转成16进制的字符串数字，提供之后的解析使用  
```javascript
//获取文件
upload() {
  let self = this;
  dialog.showOpenDialog(
  {
    title: "class文件", filters: [{name: 'class', extensions: ['class', 'CLASS']}]
  }, (filePath) => {
    if (filePath) {
      getBinaryInfo(filePath[0], (buf) => {
        var offset = 0;
        //转成16进制数组，放到hexArray里面
        while (offset < buf.length) {
          let s = buf[offset].toString(16);
          if (s.length === 1) {
            s = "0" + s;
          }
          this.hexArray.push(s);
          offset++;
        }
        self.buildTree()
      })
    }
  })
}
//使用fs模块读取文件
getBinaryInfo(path,collback){
    fs.readFile(path,function(err, bytes) {
        if (err) {
            console.log("读取文件失败")
        } else {
            var buf = new Buffer(bytes); //将文件中读取的二进制数据，存入一个buffer对象
            collback(buf);
        }
    })
}
```

