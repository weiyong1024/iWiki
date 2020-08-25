# 工具类及常用算法

内容提要：

**Java语言的基础类**，**字符串及日期**，**集合**，**排序与查找**，**泛型**，**常用算法**

## Java 中的基础类

Java 基础类库：

* `java.lang` Java 语言核心类库
    * Java 是自动导入 `java.lang.*` 的
* `java.util` 实用工具
* `java.io` 标准输入/输出类库
* `java.awt`, `javax.swing` 图形用户界面（GUI）的类库
* `java.net` 网络功能的类库
* `java.sql` 数据库访问的类库

文档：[在线 JDK API 文档](https://docs.oracle.com/javase/8/docs/api/index.html)，[文档下载](https://www.oracle.com/java/technologies/javase-jdk8-doc-downloads.html)，[更多文档](https://docs.oracle.com/javase/8/docs/index.html)

同时也可以阅读 **JDK 源码** ，一般位于 JDK 目录下的 source.zip 文件

### Object 类

`Object` 是所有类的直接或间接父类。他的存在 **保证了所有类的一致性**。

1. **`equals()` 方法**

简单地说，`equals()` 判断内容，`==` 判断引用：
```java
Integer one = new Integer(1);
Integer anotherOne = new Integer(1);
if (one == anotherOne) {    // false
    // ...
}
if (one.euqals(anotherOne)) {   // true
    // ...
}
```

如果覆盖 `equals()` 方法，一般也要覆盖 `hashCode()` 方法。目标是让 "equal" 的对象 "hashCode" 尽量相等。

2. **`getClass()` 方法**

`getClass()` 是一个 `final` 方法，不能被重写（覆盖）。

它返回一个对象在运行时所对应的类的表示：
```java
void printClassName(Object obj) {
    System.out.println("The object's class is " + obj.getClass().getName());
}

Object createNewInstanceOf(Object obj) {
    return obj.getClass().newInstance();
}
```

3. **`toString()` 方法**

`toString()` 方法用来返回对象的字符串表示。

常用于显示，例如下面的打印函数实质上就调用了对象的 `toString` 方法：
```java
System.out.println(person);
```

也可用于字符串的 `+` 号
```java
"current person is " + person;
```

通过重载 `toString()` 方法可以适当显示对象信息以进行调试。

4. **`finalize()`**

在垃圾收集前清除对象。

5. **`notify()`, `notifAall()`, `wait()`**

与线程相关的函数。

### 基本数据类型的包装类

为了适应面向对象的环境，Java 的基本数据类型也提供了包装类（wrapper）。它们是这些基本数据类型面向对象的代表。

与 8 种基本数据类型相对应，基本数据类型的包装类也有 8 种：`Character`, `Byte`, `Short`, `Integer`, `Long`, `Float`, `Double`, `Boolean`。

包装类有以下特点：

* 一些常数，例如：`Integer.MAX_VALUE`, `Double.NaN`, `Double.POSITIVE_INFINITY`
* 与字符转相互转换的函数：`valueOf(String)`, `toString()`
* 通过 `xxxValue()` 方法可以得到所包装的值，例如：`Integer` 对象的 `intValue()` 方法
* 对象中所包装的值是不可改变（immutable）的。如要改变只能生成新的对象，由此保证对象本身的 **稳定性**。
* `toString()`, `equals()` 等方法进行了覆盖

除了以上特点，有的类还提供了一些实用的方法以便操作。例如，`Double` 类就提供了 `parseDouble()`, `max`, `min` 方法等。

在 JDK1.5 以上提供了 **包装（boxing）与拆包（unboxing）** 功能：
```java
Integer I = 5;  // Integer I = Integer.valueOf(5);
int i = I;  // int i = I.intValue();
```

### Math 类

封装了一些数学上常用的 **静态函数** 和 **静态常量**。

### System 类

在 Java 中，系统属性可以通过环境变量来获得。

* `System.getProperty(String name)` 方法获得特定的系统属性值。
* `System.getProperties()` 方法获得一个 `Properties` 类的对象，其中包含了所有可用的系统属性信息。

在命令行运行 Java 程序时可使用 `-D` 选项添加新的系统属性。
```
java -D var=value MyProg
```


## 字符串和日期

字符串可以分为两大类：

* **`String` 类**：一经创建无法修改，即 immutable
* **`StringBuffer`, `StringBuilder` 类**：创建之后允许再做修改
    * 其中 `StringBuilder` 是 JDK1.5 增加的，它是 **非线程安全的**，所以执行效率更高

特别注意：在循环中使用 `String` 的 `+=` 可能会带来效率问题。因为 immutable 的特性决定它只能生成一个新的实例。

### String 类

`String` 类对象保存不可修改的 Unicode 字符序列

* `String` 类的下述方法能创建并返回一个新的 `String` 对象实例：`concat`, `replace`, `replaceAll`, `substring`, `toLowerCase`, `toUpperCase`, `trim`, `toString`
* 查找：`endsWith`, `startsWith`, `indexOf`, `lastIndexOf`
* 比较：`equals`, `equalsIgnoreCase`
* 字符及长度：`charAt`, `length`

此外，JDK1.5 增加了 `format` 函数。

除了 immutable 的特点外，还要注意 String **常量** 的内部化（interned）问题。即同样的字符串常量是 **合同**（指向同一个引用）的。

这保证了`"abc" == "abc"`，但 `"abc" != new String("abc")`

### StringBuffer 类

`StringBuffer` 类对象保存可修改的 Unicode 字符序列，`StringBuilder` 类似，它效率更高，不用考虑线程安全性。

构造方法：`StringBuffer()`, `StringBuffer(int capacity)`, `StringBuffer(String initialString)`

实现修改操作的方法：`append`, `insert`, `reverse`, `setCharAt`, `setLength`

### 字符串的分割

`java.util.StringToken` 类提供了对字符串进行分割的功能。

构造：`StringTokenizer(String str, String delim);`

该类的重要方法有：
```java
public int countTokens();   // 分割串的个数
public boolean hasMoreTokens(); // 是否还有分割串
public String nextToken();  // 得到下一个分割串
```

`String` 类的 `matches`, `replaceAll`, `split` 可以使用正则表达式。

### 日期类

`Calendar` 是 Java 内置的日历类，`Calendar.getTime()` 得到一个 `Date` 类的实例，`Data.getTime` 得到一个 `long`。

`SimpleDateFormat("yyyy-MM-dd HH:mm:ss")` 类有 `.format`, `.parse` 方法用来格式化、解析时间格式。

Java8 中引入了 time api
```java
import java.time.*;
import java.time.format.*;
```
其中，主要的类有：

* `Instant` 时刻，`Clock` 时区，`Duration` 时间段
* 常用的类 `LocalDateTime`, `LocalDate`, `LocalTime`，它们都支持 `.of`, `.parse`, `.format`, `.plus`, `.minus` 方法
* `DateTimeFormatter`



## 集合

## 排序与查找

## 泛型

## 常用算法
