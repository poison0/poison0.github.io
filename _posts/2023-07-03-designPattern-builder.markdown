---
layout: post
title: java设计模式代码-建造者模式
keywords: 技术,java
date: 2023-07-02
Author: AzureWind
tags: [java,设计模式]
comments: true
---

## 建造者模式
#### 建造者实现类  
```java
public class Course {
    private String name;
    private String ppt;
    private String video;

    public Course(String name, String ppt, String video) {
        this.name = name;
        this.ppt = ppt;
        this.video = video;
    }

    @Override
    public String toString() {
        return "Course{" +
                "name='" + name + '\'' +
                ", ppt='" + ppt + '\'' +
                ", video='" + video + '\'' +
                '}';
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPpt() {
        return ppt;
    }

    public void setPpt(String ppt) {
        this.ppt = ppt;
    }

    public String getVideo() {
        return video;
    }

    public void setVideo(String video) {
        this.video = video;
    }

    public static class CourseBuild {
        private String name;
        private String ppt;
        private String video;

        public CourseBuild buildName(String name) {
            this.name = name;
            return this;
        }
        public CourseBuild buildPpt(String ppt) {
            this.ppt = ppt;
            return this;
        }
        public CourseBuild buildVideo(String video) {
            this.video = video;
            return this;
        }
        public Course build() {
            return new Course(name, ppt, video);
        }
    }
}

```

#### 测试类  
```java
public class App 
{
    public static void main( String[] args )
    {
        Course.CourseBuild courseBuild = new Course.CourseBuild();
        Course course = courseBuild.buildName("java").buildPpt("ppt").buildVideo("video").build();
        System.out.println(course);

    }
}
```
#### 输出结果  
```
Course{name='java', ppt='ppt', video='video'}
```
