# 简单的 Java 程序

## Java 程序的类型与基本构成

Java 程序有很多种类型。而在 Java SE 中主要有两种类型： **Application** 和 **Applet**，二者的结构和运行环境有所不同：

* Application：前者是独立程序，需要执行器（JVM）来运行
* Applet：嵌在 HTML 网页中的非独立程序，可以由专门的 Applet View 来运行，也可以由 Web 浏览器（调用 JMV）来运行

### Application

HelloWorld.java
```java
public class HelloWorldApp {
    public static void main(String args[]) {
        System.out.println("Hello World!");
    }
}
```

要点：

* class 是主体
* public 类名与文件同名
* main() 的写法是固定的

### Applet

* 没有入口函数 main，故为非独立运行。
* 字节码文件 .class 需要借助支持Java的浏览器来运行。

## 程序的编写、编译和运行

* 编辑：纯文本文件 .java —— 注意文件名要和 public 的类名一致。
* 编译：JDK 中的 **javac** 工具将源文件（.java）转换为字节码文件（.class） —— 字节码文件中包含的是 JVM 指令。
* 运行：JDK 中的 **java** 工具 —— 注意 `java` 工具后跟的是 **类** 不是 **文件**。

### 设定 classpath

在 `java` 及 `java` 命令行上使用 `-classpath/-cp` 选项可以指定使用的类库：
```
javac -cp libxx.jar Source.java

java -cp libxx.jar Source
```

### 使用 package 时的编译

实际目录结构要与 ·package· 关键字后跟的文件路径一致。

编译及运行：
```
# Use -d option to make the dir for target classes. 
javac -d classes path/to/dir1/*.java path/to/dir2/*.java path/to/dir3/*.java

# Use -cp option to specify the dir in which to find target class.
java -cp classes path.to.target.Class
```

## Applet 程序的编译和运行

Java Applet 程序必须嵌入到 HTML 中，并由负责解释 HTML 文件的 WWW 浏览器充当解释器，解释执行程序。

Java Applet 在 WWW 中引入了动态交互的内容。

在编译得到 .class 文件后，使用 HTML 中的 <applet> 标签来嵌入 Applet。

可以使用　**appletViewer** 来执行内嵌　Applet 的 HTML 文件。
```
appletViewer xxx_including_applet.html
```

*从 Java8 开始，`Applet` 的运行受到了严格的限制，由此也诞生了很多替代方案，如 `Flash`、`SilverLight` 等。后来更直接的直接使用 JavaScript 或 HTML5 里的一些功能来实现网页里面的交互功能。*

## JDK中的其他几个工具

* javac：编译
* java：运行（终端 或 GUI）
* appletViewer：运行 Applet 程序
* jar：打包工具
* javadoc：生成文档
* javap：查看类信息及反汇编
* ...

### 使用 jar 打包

打包
````
javac -cvfm A.jar A.man A.class
````
其中 `c`（create 创建），`v`（verbose 详情），`f` 为输出文件名，`m` 为清单文件名。

运行
```
java -jar A.jar
```

一般清单文件会写明 jar 包的 **版本**、**根路径** 和 **主类名**
```
Manifest-Version: 1.0
Class-Path: .
Main-Class: A
``` 

注：清单文件可以任意命名，常见用法是 MANIFEST.MF。

### 使用 javadoc 生成文档

```
javadoc -d 目录名 xxx.java
```

`/**    */` 中的内容可以用一下标记：

* `@author`：类说明；标明开发该类模块的作者
* `@version`：类说明；标明该类模块的版本
* `@see`：类、属性、方法的说明；参考专项，即相关主题
* `@param`：方法说明；对方法中某参数的说明
* `@return`：方法说明；对方法返回值的说明
* `@exception`：方法说明，对方法可能跑出异常的说明

### Java API 的文档

官方文档：https://docs.oracle.com/javase/8/docs/api/index.html

### javap 产看类信息和反汇编

查看类信息
```
javap 类名
```

反汇编
```
javap -c 类名
```
输出的是 JVM 指令。
