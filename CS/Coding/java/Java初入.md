---
title: Java学习笔记——基础1
date: 2017-07-01 18:40:59
categories: 
    - 编程与生活
tags: 
    - Java
---

> (因为我粗略学过C,C++,Python，了解过他们的一些语法，所以为了使得java的入门更为顺畅，便会忽略一些和C语法相类似的地方，着重点明一些java自己的特色之处。也减轻一下自己写文字的负担。)

JAVA是一门[静态的强类型](https://www.zhihu.com/question/19918532)的面向对象的重量级的编程语言。

一般写JAVA程序需要的组件是JDK，到甲骨文的网站上下载即可。

初级阶段使用文本编辑器即可，当然，我正处在这个阶段。

此时用来编译运行JAVA文件常用的两条命令是

```bat
javac -encoding utf-8 filename.java
java filename
```

以上命令就是先使用`javac`在java文件当前目录下，编译生成字节码文件`filename.class`，之后再用`java`解释执行成特定平台的机器码，输出。
这里指定了编码格式，因为java默认文件为unicode编码，但是文件中有中文，会有错误提示。

```bat
错误：编码GBK的不可映射字符
```

若是你没有这个问题，则可以忽略那个`-encoding utf-8`

---

## java源文件的命名

和c，c++，python不同，java要求，源文件后缀名为`.java`，文件名字一般要求和文件中的 *public class* 同名，若是没有定义，那就没有限制了。这也可以看出，**一个java源文件中只能最多有一个 *public class***。通常建议一个java源文件只定义一个类，让java的源文件主文件名与该源文件中定义的 *public class*同名。

## java程序的结构

*java程序必须以类的形式存在*，类是java程序的最小单位，java不允许除类外的可执行语句，方法等成分独立存在。他们必须放在类定义里。

例子：

```java
public class HelloWorld
{
    public static void main(String[] args)
    {
        System.out.println("Hello World");
    }
}
```

观察上例，与文件同名的 *public class* `HelloWorld`，内部包含一个`main()`方法，使用`System.out.println("Hello World");`实现对于文本的输出，类似的有个`System.out.print("Hello World");`，差别就在于前者会换行。

目前用到的`main()`函数都是这样写的，关于详细内容之后的文章会介绍。

*若是存在多个类怎么办？*

可见下例：

```java
class Dog{
    public void jump(){
        System.out.println("正在执行jump方法");
    }
    public void run(){
        this.jump();
        System.out.println("正在执行run方法");
    }
}

public class DogTest{
    public static void main(String[] args){
        Dog dog = new Dog();
        dog.run();
    }
}
```

因为只允许有一个`public class`，故而其他的就是普通的类的形式。

## java中的流程控制语句

    if (...){
        ...
    }
    else if{
        ...
    }
    else{
        ...
    }

    switch (...){
        case ...:{
        ...
        break;
        }
        ...
        default:{
        ...
        }
    }

    while (...){
        ...
    }

    do{
        ...
    }while (...);

    for (...; ...; ...){
        ...
    }

Java中出现了一种迭代式的for的用法。python和C++都有类似的语法结构。

    for ( type variableName : array | collection){
        // variableName 自动迭代访问每个元素
    }

例如：

```java
public class ForEachTest
{
    public static void main(String[] args)
    {
        String[] books = {"轻量级",
                        "fengkuang",
                        "jiangyi"};
        // 使用foreach迭代遍历。
        for(String book : books)
        {
            System.out.println(book);
        }
    }
}
```
