# 异常机制

异常的本质是由程序员通过编程来提高程序的鲁棒性（容错性）。

## 异常处理

基本写法
```java
try {
    // 程序逻辑
} catch (Exception1 ex) {
    // 异常处理
} catch (Exception2 ex) {
    // 异常处理
} finally {
    // 异常处理
}
```
其中，`catch` 语句可以有零个至多个，可以没有 `finally` 语句

Java 中的异常处理流程是：

* **抛出（throw）** 异常
* **运行时系统在调用栈中** 查找异常
    * 从异常生成的方法开始 **回溯**，直到找到；
* **捕获（catch）** 异常的代码

Java 中 `Throwable` 是所有异常的父类，它下面分成两类：

* `Error`：JVM 错误
* `Exception`：异常

一般我们所说的异常是指 `Exception` 及其子类

### Exception 类

构造方法：
```java
public Exception();
public Exception(String message);
Exception(String message, Throwable cause);
```

方法：
```java
getMessage()
getCause()
printStackTrace()
```

### 多异常处理

子类异常要排在父类异常前面。

**`finally` 语句无论是否有异常都要执行**，即使其中有 `break`, `return` 等语句。在编译时，`finally` 部分的代码生成了多遍。

### 受检的异常

Java 中的异常分两种：

* `RuntimeException` 及其子类，可以不明确处理（一般使用 `if` 来语句判断）
* 否则，称为 **受检的异常**（chected Exception），例如 IO 异常

受检的异常，要求 **明确进行语法处理**

* 要么捕获（catch）
* 要么抛出（throw）：在方法签名的后面用 `throws xxxx` 来声明
    * 在子类中，如果要覆盖父类的一个方法，若父类的方法声明了 `throws` 异常，则子类的方法也可以 `throws` 异常。
    * 可以跑出子类异常（更具体的异常），但不能抛出更一般的异常。

一种语法糖（Compiler suger）：`try ... with ... resource`
```java
try (类型 变量 = new 类型() ) {
    // ...
}
```
这里编译期自动添加了 `finally { 变量.close(); }`，无论是否出现异常都会执行。

类似于 Python 里面的 `with` 语句。


## 自定义异常类

继承自 `Exception` 及其子类，可以重载父类方法也可以自定义方法。

### 重抛异常及异常链接：

对于异常，优势光捕获是不够的，还需要将其进一步传递给调用者，以便让调用者也能感受到这个异常。这是可以在 `catch` 语句块或 `finally` 语句块中采取以下三种方式：

* 当前捕获的异常再次抛出：
```java
throw e;
```
* 重新生成并抛出一个异常：
```java
throw new Exception("Some message");
```
* 重新生成并抛出一个异常，该异常中包含了当前异常的信息，如：
```java
throw new ExceptioN("Some message", e);
```
可以用 `e.getCause()` 来得到内部异常。


## 断言（assertion）

`assert` 的格式是：

* `assert` 表达式;
* `assert` 表达式: 信息;

在调试程序时，如果表达式的值不为 `true`，则程序会产生异常，并输出错误信息。

### assert 的编译和运行

**编译**：只有在 JDK1.4 及以上版本中才能使用断言。

具体地说，在早期的 JDK 版本（1.4）中编译时，要通过 `-source` 选项来指明版本，如：
```
javac -deprecation -source 1.4 -classpath . XXX.java
```

**运行**：在运行时，要使 `assert` 语句起作用，需要加上 `-ea / -enableassertions` 选项，如：
```java
java -ea -classpath . XXX
```

### 测试及 JUnit

程序的修改是经常要进行的过程，而测试保证了程序在每次修改之后的正确性。

在编写程序代码的同时，还编写测试代码来判断这些程序是正确的。

这个过程称为 **测试驱动开发**

从而保证了代码质量，减少了后期的查错和调试时间，所以实际上它大幅提高了程序的开发效率。

通常使用 JUnit 框架来进行 Java 程序的测试，现在大多数 IDE 都集成了对 JUnit 的支持。

* 用 `@Test` 来标注测试函数
* 在测试中常用的语句如下：
    * `fail(信息);`：程序出错
    * `assertEqual(参数1, 参数2);`：程序要保证两参数相等
    * `assertNull(参数);`：表示参数要为 `null`
    ```java
    @Test
    public void testSum() {
        // ...
    }
    ```

## 程序的调试

程序中的错误可以分成三大类：

* **语法错误**（Syntax error）：人在编辑的时候发现、编译器发现
* **运行错误**（Runtime error）：异常处理机制
* **逻辑错误**（Logic error）：调试（debug）、单元测试（UT）

调试（debug）的常用手段：**断点（breakpoint）**、**跟踪（trace）**、**监视（watch）**

当然，**调用堆栈**（call stack）也能给我们提供很多信息。

