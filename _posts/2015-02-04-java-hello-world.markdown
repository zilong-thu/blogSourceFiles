---
title: Java Hello World
date: 2015-02-04 10:32:00
tags: Java
categories: Java
sidebar: false
---

> 本人擅长用各种编程语言实现“Hello World”程序。

参考书籍：李刚, 《疯狂Java讲义（第三版）》, 北京：电子工业出版社, 2014.

<!-- more -->

###环境变量配置

环境变量的设置很重要。Windows中的配置如下：

```
%JAVA_HOME%    C:\Java\jdk1.7.0_25
```

```
CLASSPATH    .;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar
```

```
PATH    %JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;
```

如果这样设置后运行`java`或`javac`命令还不能生效，可以试试重启电脑。

###Hello World

```
/* 文件命名为 hello.java */
public class hello{
    public static void main(String[] args){
        System.out.println("Hello world!");
    }
}
```

在该目录下打开命令行，运行

```
javac hello.java
```

这样可以生成字节码 `hello.class`文件。然后运行

```
java hello
```
于是就可以 Hello World 了。

###Using Package

```java hello.java
package com;  /**/

public class hello{
    public static void main(String[] args){
        System.out.println("Hello world!");
        Person me = new Person();
        me.name = "wzl";
        me.saysomething();
    }
}

class Person{
    public String name;
    public int age;
    public void run(){
        System.out.println("I am running...");
    }
    public void saysomething(){
        System.out.println("Daddy is "+name);
        run();
    }
}
```
在`hello.java`的同目录下运行：

```
javac -d . hello.java
```

这样会生成一个名为`com`的新目录，里面包括两个`.class`文件：`hello.class`和`Person.class`。

要运行这个hello程序，在`com`的上一级目录下：

```
java com.hello
```

而进入`com`目录后，无论运行`java com.hello`，还是运行`java hello`，都会失败。

> Java的包机制需要两个方面保证：
> + 源文件里使用package语句指定包名；
> + `class`文件必须放在对应的路径下。

###多态

```java sub.java
/* 关于多态 */
class base{
    public int name = 1;
    public void base(){
        System.out.println("base method.");
    }

    public void test(){
        System.out.println("method to be overriden.");
    }
}

public class sub extends base{
    public String name = "i am sub class";
    public void test(){
        System.out.println("test method from sub class.");
    }

    public void sub(){
        System.out.println("sub method");
    }

    public static void main(String[] args){
        /* 把子类的对象赋值给父类的引用变量 */
        base bc = new sub();
        System.out.println(bc.name);  /* 输出：  1 。因为实例变量并不具备多态性。*/
        bc.base();  /* 输出：  base method. */
        bc.test();  /* 输出：  test method from sub class. */
    }
}
```

###若干基本名词
+ __JVM__：Java Virtual Machine，Java虚拟机。Java编译器将Java程序编译为平台无关的字节码，这些字节码不面向任何具体平台，只面向JVM。不同平台的JVM是不同的，但对字节码的接口是相同的。
+ __JRE__：Java Runtime Environment，Java运行时环境
+ __JDK__：Java SE Development Kit，Java标准版开发包，是Sun公司提供的一套用于开发Java应用程序的开发包，它提供了编译、运行Java程序所需的各种工具和资源，包括Java编译器、Java运行时环境，以及常用的Java类库等。
