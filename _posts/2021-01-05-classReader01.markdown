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
3 本文参考了《Java虚拟机规范（Java SE 8版）》《深入理解Java虚拟机（第3版）》。  
4 选择前端来实现，是因为前端写页面比较简单，效果也要比java页面要好看。  

<!-- more -->

### 使用electron的 dialog 模块选择文件，并用node的fs模块读取文件二进制
extensions表示扩展名，title是弹出框的title。  
选中之后转成16进制的字符串数字，提供之后的解析使用。
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
### 分析class文件结构
  class文件内信息是以非常紧凑的方式存放到一起的，并且严格按照一定顺序，所以解析的时候只要按照规范顺序读取即可，每个class文件结构如下所示。  
  u1,u2,u4,u8分别代表这个字段占用的字节数为1，2，4，8个。
  ```
  ClassFile{
    u4               magic;  魔数
    u2               minor_version;  次版本号
    u2               major_version;  主版本号
    u2               constant_pool_count;    常量池个数
    cp_info          constant_pool[constant_pool_count];  常量池
    u2               access_flags;   类访问标志
    u2               this_class; 类索引
    u2               super_class;    父类索引
    u2               interfaces_count;   接口计数器
    u2               interfaces[interfaces_count]; 接口表
    u2               fields_count;   字段计数器
    field_info       fields[fields_count]; 字段表
    u2               methods_count;  方法计数器
    methods_info     methods[methods_count];    方法表
    u2               attribute_count;    属性表计数器
    attribute_info   attribute_info[attribute_count]; 属性性表
  }
```
解析方式是把解析后代文件放到一个对象里，因为class二进制文件是严格按照顺序排列的，所以只需要按照顺序解析即可，建立一个classFile对象如下
```javascript
 
    hexArray: [], //16进制数组
    classFile: {
        magic: {},//魔数
        minor_version: {},//次版本号
        major_version: {},//主版本号
        constant_pool_count: {},//常量池个数
        constant_pool: [],//常量池
        access_flags: {},//类访问标志
        this_class: {},//类索引
        super_class: {},//父类索引
        interfaces_count: {},//接口计数器
        interfaces:[],//接口表
        fields_count:{},//字段计数器
        fields:[],//字段表
        methods_count:{},//方法计数器
        methods:[],//方法表
        attribute_count: {},//属性表计数器
        attribute_info:[]//属性表
    },
    readIndex: 0,//解析时读取的指针
```
按照顺序执行，每读取一个字节，指针就加一，指针始终指向下一个待读的字节。

创建一个函数用来读取固定长度字节，并生成一个固定的对象返回，读取对象时只需要调用此方法即可。

```javascript
 //获取u1，u2，u4，u8字段
getUFields(type, typeName, value) {
    let start = this.readIndex;
    let array = this.hexArray;
    let u = {
        startIndex: start,
        endIndex: 1,
        hexArray: [],
        typeName: typeName,
        type: "u1",
        length: 1
    };
    switch (type) {
        case 1:
            u.hexArray = array.slice(start, start + 1)
            u.endIndex = start + 1;
            u.type = "u1";
            this.readIndex += 1;
            break
        case 2:
            u.hexArray = array.slice(start, start + 2)
            u.endIndex = start + 2;
            u.type = "u2";
            this.readIndex += 2;
            break
        case 4:
            u.hexArray = array.slice(start, start + 4)
            u.endIndex = start + 4;
            u.type = "u4";
            this.readIndex += 4;
            break
        case 8:
            u.hexArray = array.slice(start, start + 8)
            u.endIndex = start + 8;
            u.type = "u8";
            this.readIndex += 8;
            break
    }
    if (!value) {
        //转成10进制值
        u["value"] = Number(eval("0x" + u.hexArray.join("")).toString(10));
    }
    return u;
},
```