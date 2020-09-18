---
layout: post
title: 目录过滤器FilenameFilter简单用法
category: 技术
keywords: 技术,java,gc
---
FilenameFilter接口存在的唯一原因就是过滤文件列表中的文件名，在调用path.list()方法中传入FilenameFilter的实现类即可。重要的是重写accept方法，方法返回true会被添加列表中，否则会被筛掉

## 筛选文件夹内以java结尾的文件名
```java
public class FilenameFilterTest {
    public static void main(String[] args) {
        File path = new File("./src/test_12");
        String[] list = path.list(new Filter("\\S+java"));
        for (String s : list) {
            System.out.println(s);
        }
    }
}
class Filter implements FilenameFilter {
    private Pattern pattern;
    public Filter(String regex) {
        this.pattern = Pattern.compile(regex);
    }
    @Override
    public boolean accept(File dir, String name) {
        return pattern.matcher(name).matches();//根据传入的正则表达式判断是否存在文件名
    }
}
```
##用lambda表达式可以简写成如下形式
```java
public class FilenameFilterTest {
    public static void main(String[] args) {
        File path = new File("./src/test_12");
        String regex = "\\S+java";
        String[] list = path.list((File dir, String name)-> Pattern.compile(regex).matcher(name).matches());
        for (String s : list) {
            System.out.println(s);
        }
    }
}
```
