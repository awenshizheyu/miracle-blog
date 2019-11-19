---
title: 05-ApacheCamel系列文章-快速入门（HelloWorld）
categories: 
- ApacheCamel系列文章
---
程序员的世界里，最亲切的一句话，莫过于：“Hello World”，今天，我们通过一个简单的例子，快速入门。

## 目的
本例子非常简单，就是通过Camel实现一个文件的搬运。
假设源文件夹目录：
```
/Users/awen/Desktop/from
```
假设目标文件夹目录：
```
/Users/awen/Desktop/to
```
那么，本例子的目的就是将 From 文件夹内的文件搬运到 To 文件夹内。
直接上代码，如下：

## 代码
```
package com.miracle.demo.helloworld;

import org.apache.camel.CamelContext;
import org.apache.camel.builder.RouteBuilder;
import org.apache.camel.impl.DefaultCamelContext;

public class Main {

    private static final String FROM_PATH = "/Users/awen/Desktop/from";
    private static final String TO_PATH = "/Users/awen/Desktop/to";

    public static void main(String[] args) throws Exception {
        CamelContext context = new DefaultCamelContext();
        RouteBuilder routeBuilder = new RouteBuilder() {
            @Override
            public void configure() throws Exception {
                from("file:" + FROM_PATH)
                        .to("file:" + TO_PATH);
            }
        };
        context.addRoutes(routeBuilder);
        context.start();
        Thread.sleep(Integer.MAX_VALUE);
        context.stop();
    }
}
```

代码解释如下：
```
private static final String FROM_PATH = "/Users/awen/Desktop/from";
private static final String TO_PATH = "/Users/awen/Desktop/to";
```
定义两个常量，
FROM_PATH：源文件的路径。
TO_PATH：目的文件的路径。

```
CamelContext context = new DefaultCamelContext();
```
创建一个Camel的上下文，这里的上下文有点类似于Spring的上下文，
包含了Camel中的路由信息，Endpoint信息，Component信息，状态管理等，
这里只是采用自带的一个默认的上下文：DefaultCamelContext。

```
RouteBuilder routeBuilder = new RouteBuilder() {
    @Override
    public void configure() throws Exception {
        from("file:" + FROM_PATH)
                .to("file:" + TO_PATH);
    }
};
```
这是关键代码，通过路由的构建器构建一个新的路由，
需要实现 RouteBuilder 的 configure 方法，
在这个configure方法中，camel实现了特定领域的定义语言，
就是可以通过Java的方式，定义路由，如下：
```
from("file:" + FROM_PATH)
.to("file:" + TO_PATH);
```
知名见义：
from(xxxx) 从一个Endpoint开始，这里表示的是从 FROM_PATH 这个文件（file）开始。
to(xxxx) 到另一个Endpoint里去，这里表示是到 TO_PATH 这个文件夹里面去。
注： 这里的 “file” 是一个关键字，代表的这个 Endpoint 是一个文件（夹）。

```
context.addRoutes(routeBuilder);
```
把定义好的路由添加到上下文中。
```
context.start();
```
启动上下文。
```
Thread.sleep(Integer.MAX_VALUE);
context.stop();
```
睡眠足够多的时间，确保主线程不退出工作。

综上，
要实现从一个文件夹将里面的文件搬运到另外一个文件夹，
通过Camel的方式是非常简单的，最重要的就是一句话：
form("xxxx").to("xxxx");
其他的都是模板性的文件，几乎不用变。

那么，可能有人就问了，要实现：从一个文件夹将里面的文件搬运到另外一个文件夹，
为什么要用camel呢，我直接写Java就可以完成了，
干嘛还要学一个新的框架，
那么，下面，我们就用传统写Java的方式实现这件工作，如下：

## 传统方式实现
假如没有Camel，那么，我们该如何完成，代码如下：
```
package com.miracle.demo.helloworld;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;

public class Traditional {
    private static final String FROM_PATH = "/Users/awen/Desktop/from";
    private static final String TO_PATH = "/Users/awen/Desktop/to";

    public static void main(String[] args) throws Exception {

        File fromDir = new File(FROM_PATH);
        File toFileTemp = null;
        FileInputStream fileInputStream = null;
        FileOutputStream fileOutputStream = null;
        String fileNameTemp = null;
        int readCountTemp = -1;
        while (true) {

            for(File fileTemp : fromDir.listFiles()) {
                // 列出当前目录下所有文件，并逐个处理。

                fileNameTemp = fileTemp.getName();
                toFileTemp = new File(TO_PATH + File.separator + fileNameTemp);
                if(!toFileTemp.exists()) {
                    toFileTemp.createNewFile();
                }
                fileOutputStream = new FileOutputStream(toFileTemp);
                fileInputStream = new FileInputStream(fileTemp);
                byte[] byteTemp = new byte[100];
                // 读写源文件内容，并写入新文件
                while ((readCountTemp = fileInputStream.read(byteTemp)) != -1) {
                    fileOutputStream.write(byteTemp, 0, readCountTemp);
                }
                // 删除源文件
                fileTemp.delete();

                // 关闭相关的数据流，并将临时变量置空。
                fileInputStream.close();
                fileOutputStream.close();
                fileInputStream = null;
                fileOutputStream = null;
                toFileTemp = null;
                fileNameTemp = null;
                readCountTemp = -1;
            }
            // 3秒钟进行一次扫描
            Thread.sleep(3000);
        }
    }
}
```

代码都比较简单，
整体思路就是，扫描源文件夹，然后取出底下所有的文件，
挨个轮询，把每个文件搬到目的文件。
但是，存在以下问题：
1. 代码复杂度：可以看到，代码复杂度较高，需要自己编写的内容较多。
2. 功能：上面的例子其实很多内容没考虑，比如说，文件流关不上怎么办，文件夹下还有文件夹怎么办，轮询间隔时间怎么动态配置，多线程搬运文件怎么做，等等等等，我们仅仅只是实现这么一个小功能，就写了这么多行，如果把上面这些问题考虑，那么，这个代码将是灾难性的。

但是，
用了Camel就很简单了，
Camel把一切事情都考虑清楚了，
并且，
这只是对Camel的file组件的应用，
Camel现在包含了100多种组件，
可以无缝的集成各个组件，包括了 sql、ActiveMQ、hdfs、jms等等等等。

相信我，她是一个好东西，你会喜欢的。

END