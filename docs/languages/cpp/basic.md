# C++基础

## 变量定义

### `auto`变量

由编译器根据上下文自动确定变量类型，如：
```cpp
auto i = 3;
auto f = 4.0f;
```

### 指针变量的动态生成和删除
    
* 指针变量所指内存可以通过`new/delete`运算符在程序运行时动态生成和删除，如：
```cpp
int* ptr = new int;
int* array = new int[10];
delete ptr;
delete[] array;
```

### 左值引用

* 具名变量的别名：类型名& 引用名 变量名
```cpp
int v0;
int& v1 = v0;
```
这里`v1`是`v0`的引用，他们在内存中是同一单元的两个不同名字

* 引用必须在定义时进行初始化（赋初值）。

* 被引用变量名可以是结构变量成员，如`s.m`。

* 函数参数可以是引用类型，表示函数的形式参数与实际参数是同一个变量，改变形参将改变实参。  
如调用以下函数将交换实参值：
```cpp
void swap(int& a, int& b) {
    int tmp = b;
    b = a;
    a = tmp;
}
```

* 函数的返回值可以是引用类型，但不能是函数的临时变量。

### 右值引用（C++11 引入）

* **右值**：不能取地址的、没有名字的就是右值

* 匿名变量（临时变量）的别名：类型名&& 引用名 表达式，如
```cpp
int&& sum = 3 + 4;
float&& res = ReturnRvalue(f1, f2);
```

* 右值引用的典型应用是在函数参数中，目的是减少临时变量的拷贝开销，例如：
```cpp
void AcceptRvalueRef(T&& s) {...}
```


## 变量初始化、类型推导、基于范围的循环

### 初始化列表

* 用`{}``包括起来的元素序列，可以用来对变量进行初始化，如
```cpp
int a[] = {1, 3, 5};
int a[] {1, 3, 5};
```
初始化列表可以再`{}`之前使用`=`，也可以不用。

* 变量的初始化方式
```cpp
int a = 3 + 5;
int a = {3 + 5};
int a(3 + 5);   // 调用int的构造函数
int a{3 + 5};
int* i = new int(10);
double* d = new double{1.2f};   // 初始化列表
```

### 类型推导

使用`decltype`可以对变量或表达式结果的类型进行推导，如
```cpp
struct {
    char* name;
} anon_u;

struct {
    int d;
    decltype(anon_u) id;
} anon_s[100];  // 匿名的struct数组

int main() {
    decltype(anon_s) as;
    cin >> as[0].id.name;
}
```

### 基于范围的`for`循环语句

在循环头的圆括号中，由冒号`:`分为两部分，第一部分适用于迭代的变量，第二部分则表示被迭代的范围，如：
```cpp
int arr[3] = {1, 3, 5};
for (int e : arr)
    //...
```

## 函数

### 函数重载

同一名称的函数，有两个以上不同的函数实现，被称为“函数重载”，如：
```cpp
void print(char* msg) {
    cout << "message: " << msg << endl;
}

void print(int score) {
    cout << "score: " << score << endl;
}
```

函数重载要求函数形参不同，不能出现仅仅返回值不同的情况。编译器通过函数调用语句的实参确定哪一个函数被调用。

多个同名函数实现之间，必须保证至少有一个函数参数的类型有区别。返回值、参数名称等不能作为区分标识。

### 函数参数的缺省值

函数参数可以在定义时设置默认值（缺省值），这样在调用该函数时，若不提供相应的实参，则编译器自动将相应形参设置成缺省值，如：
```cpp
void print(char* msg = "hello") {
    cout << msg << '#';
}

int main() {
    print("Beijing...");
    print();
    return 0;
}
```
输出：
```
Beijing...#hello#
```

* 带缺省值的函数参数必须放在没有缺省值的函数参数后面。

### 追踪返回类型的函数

可以将函数返回类型的信息放到函数参数列表的后面进行声明，如：

* 普通函数声明形式：
```cpp
int func(char* ptr, int val);
```

* 追踪返回类型的函数声明形式：
```cpp
auto func(char* ptr, int val) -> int;
```

* 追踪返回类型在原本函数返回值的位置使用`auto`关键字。

* **动机**：有时函数在定义的时候并不能确定返回值类型，而需要通过`decl`获取参数的类型来确定。

* **应用**： 在C++模板的定义中，有时返回类型需要根据参数类型类确定，会用到这个特性。

## 类

### 用户自定义的类型——类：

* 一种用户自定义的类型，包含函数与数据的特殊“结构体”，扩充C++语言的类型体系。

* 类中包含的函数，称为 **成员函数** ；包含的数据，称为 **数据成员** 。

* 类中函数及可以在类中给出定义，也可以在类外给出定义。

* 类的成员（函数、数据）可以根据需要分成组，不同组设置不同的访问权限。

* 权限种类：`public`, `private`, `protected`。

* 类定义后，核函数内建的类型一样，用类来定义变量，该变量通常被称为 **对象** 。

* 通过“对象名.成员名”的形式，可以使用对象的数据成员，或调用对象的数据函数，但仅限于访问`public`权限的成员。

* 在类外定义成员函数时，函数名前要加上类名限定，格式为：`类名::函数名`，其中`::`称为 **域运算符** 。

在头文件中声明类`class`

```cpp
// matrix.h

#ifndef MATRIX_H
#define MATRIX_H

class Matrix {
    int data[6][6];

   public:
    void fill(char dir);
};

#endif
```

在实现文件中定义类`class`

```cpp
// matrix.cpp

#include "matrix.h"

void Matrix::fill(char dir) {
    //...
}
```

* 通常，类的声明放在头文件中，而类的成员函数实现则放在实现文件中。

* 为了便于管理和代码复用，一般是将不同的类分别保存为不同的头文件和实现文件。

### 成员函数的两种定义方式

可以在类的定义内部实现，也可以用`::`运算符在外部实现。

```cpp
class Matrix {
    public:
    void fill(char dir) {
        //...
    }
};

void Matrix::fill(char dir) {
    //...
};
```

### `this`指针

所有成员函数的参数中，隐含着一个只想当前对象的指针变量——`this`。

* 这也是 **成员函数** 与 **普通函数** 的重要区别

### 访问权限

C++规定类的成员缺省为`private`权限。

* 对象使用`.`操作符访问对象的`public`成员。

* 对象指针使用`->`操作符访问所指对象的公有成员。

#### 友元

有时需要允许某些函数访问对象的私有成员，可以通过声明该 **函数** 为 **类** 的“友元”来实现。
```cpp
class Test {
    int id;

   public:
    friend void print(Test obj);
    //...
};

void print(Test obj) { cout << obj.id << endl; }
```
`Test`类中声明了`Test`类的友元函数`print`，该函数在实现时可以访问`Test`类定义的函数对象的私有成员。

工程中一种常见的用法是将UT中的测试函数生命成被测试类的友元，以便在测试函数中访问被测试类的私有成员。


## 构造函数、拷贝构造函数与析构函数

解释如下程序的运行结果：
```cpp
#include <iostream>

using namespace std;

class Test {
   public:
    Test() { cout << "Test()" << endl; }
    Test(const Test& src) { cout << "Test(const Test&)" << endl; }
    ~Test() { cout << "~Test()" << endl; }
};

void func1(Test obj) { cout << "func1()..." << endl; }

Test func2() {
    cout << "func2()..." << endl;
    return Test();
}

int main() {
    cout << "main()..." << endl;
    Test t;
    func1(t);
    t = func2();
    return 0;
}
```

**拷贝构造函数** 会在函数由实参获得形参时调用。

输出
```
main()...
Test()
Test(const Test&)
func1()...
~Test()
func2()...
Test()
~Test()
~Test()
```


## 赋值运算符 `=` 重载

解释如下程序的运行结果
```cpp
#include <iostream>

using namespace std;

class Test {
   public:
    Test(int _id) : id(_id) { cout << "obj_" << id << "created\n"; }
    
    Test& operator= (const Test& right) {
        if (this == &right) cout << "same obj!\n";
        else {
            cout << "obj_" << id << " = obj_" << right.id << endl;
        }
        return *this;
    }

   private:
    int id;
};

int main() {
    Test a(1), b(2);

    cout << "a = a: ";
    a = a;

    cout << "a = b: ";
    a = b;

    return 0;
}
}
```

输出
```
obj_1created
obj_2created
a = a: same obj!
a = b: obj_1 = obj_2
```


## 流运算符 `<</>>` 重载

解释如下程序运行结果
```cpp
#include <iostream>

using namespace std;

class Test {
    int id;

   public:
    Test(int _id) : id(_id) {
        cout << "obj_" << id << "created\n";
    }

    friend istream& operator>> (istream& in, Test& dst);
    friend ostream& operator<< (ostream& out, const Test& src);
};

istream& operator>> (istream& in, Test& dst) {
    in >> dst.id;
    return in;
}

ostream& operator<< (ostream& out, const Test& src) {
    cout << src.id << endl;
    return out;
}

int main() {
    Test obj(1);
    cout << obj;
    
    cin >> obj;
    cout << obj;

    return 0;
}
```

* 将 **流运算符** 声明成 $Test$ 类的友元，在实现的时候可以访问其私有变量。

* 返回流对象是为了支持流运算符的链式操作。

输出（中间输入流内容为 $2$ ）
```
obj_1created
1
2
2
```


## 函数运算符 `()` 重载

解释如下程序运行结果
```cpp
#include <iostream>

using namespace std;

class Test {
   public:
    int operator()(int a, int b) {
        cout << "operator() called. " << a << ' ' << b << endl;
        return a + b;
    }
};

int main() {
    Test sum;
    int s = sum(3, 4);
    cout << "a + b = " << s << endl;

    return 0;
}
```

sum对象看上去像一个函数，故也称“函数对象”。

输出
```
operator() called. 3 4
a + b = 7
```


## 下标运算符 `[]` 和 `++/--` 自增减运算符

### 下标运算符

下面的代码体现设计模式中“包装”的思想，让原本只支持数字的`[]`运算符对外支持字符串类型索引。
```cpp
#include <iostream>
#include <string.h>

using namespace std;

char week_name[7][4] = {"mon", "tu", "wed", "thu", "fri", "sat", "sun"};

class WeekTemp {
   public:
    int& operator[](const char* name) {
        for (int i = 0; i < 7; i++) {
            if (strcmp(week_name[i], name) == 0) return temp[i];
        }
    }

   private:
    int temp[7];
};

int main() {
    WeekTemp beijing;
    beijing["mon"] = -3;
    beijing["tu"] = -1;
    cout << "Monday Temperture: " << beijing["mon"] << endl;
    return 0;
}
```

输出
```
Monday Temperture: -3
```

### 前缀`++/--`与后缀`++/--`

前缀运算符重载声明
```cpp
ReturnType operator++();
ReturnType operator--();
```

后缀运算符重载声明
```cpp
ReturnType operator++(int dummy);
ReturnType operator--(int dummy);
```

通过在函数参数中的哑元参数`dummy`来区分前缀和后缀的同名重载。

哑元：函数体语句中没有使用该参数。


## 静态成员和常量成员

### 静态成员

`static`修饰的数据成员隶属于类。

* 静态数据成员被该类的所有对象共享（即所有对象中的这个数据域处于同一内存位置）

* 静态数据成员要在 **实现文件** 中赋初值，格式为：
`Type ClassName::static_var = Value;`

对于静态成员函数，编译器不提供指向对象的指针，它们不能调用非静态成员函数。

类的静态成员既可以通过对象来访问，也可以通过类名来访问。

解释如下代码行为：

```cpp
#include <iostream>

using namespace std;

class Test {
   public:
    Test() { count++; }
    ~Test() { count--; }
    static int how_many() { return count; }

   private:
    static int count;
};

int Test::count = 0;

void print(Test t) { cout << "in print(), Test#: " << t.how_many() << endl; }

int main() {
    Test t1;
    cout << "Test#: " << Test::how_many() << endl;

    Test t2 = t1;
    cout << "Test#: " << Test::how_many() << endl;

    print(t2);

    cout << "Test#: " << t1.how_many() << ", " << t2.how_many() << endl;

    return 0;
}
```

输出
```
Test#: 1
Test#: 1
in print(), Test#: 1
Test#: 0, 0
```

注意到 `t2 = t1;` 此处调用的是`=`运算符，而`print(t2);`调用的是 **拷贝构造函数** ，但在`Test`里面均未定义行为。但 `print(t2);` 返回时调用了 **析构函数** ，故最后一行静态成员`count`变成了0。

### 常量成员

`const`修饰的数据成员，称为类的常量数据成员，在对象的整个生命周期里不可改变。

* 常量数据成员只能在构造函数初始化列表中被设置，不能在函数体中通过赋值设置。

`const`修饰的成员函数，则该成员函数在实现时不能修改类的数据成员 ———— 即静态函数不能改变对象状态。

* 若对象被定义为常量，则它只能调用以`const`修饰的成员函数，其他普通成员函数则不允许调用。

解释如下代码行为

```cpp
#include <iostream>

using namespace std;

class Test {
   public:
    Test(int id) : ID(id) {}
    int MyID() const { return ID; }
    int Who() { return ID; }

   private:
    const int ID;
};

int main() {
    Test obj1(12231031);
    cout << "ID_1 = " << obj1.MyID() << endl;
    cout << "ID_2 = " << obj1.Who() << endl;

    const Test obj2(1602401);
    cout << "id_1: " << obj2.MyID() << endl;

    return 0;
}
```

输出
```
ID_1 = 12231031
ID_2 = 12231031
id_1: 1602401
```


## 对象组合

可以在类中使用其他类来定义数据成员，通常称之为“子对象”。这种包含关系称为 **组合** ，组合关系可以嵌套。

子对象构造时若需要参数，则应在当前类的构造函数的 **初始化列表** 中进行。若使用默认构造函数来构造子对象则不用做任何处理。

对象构造与析构次序：穿脱原理

* 先完成子对象构造，再完成当前对象构造
* 先对外层对象析构，再对内层对象析构

解释如下代码行为

```cpp
#include <iostream>

using namespace std;

class C1 {
   public:
    C1(int id) : ID(id) { cout << "C1(int)" << endl; }
    ~C1() { cout << "~C1()" << endl; }

   private:
    int ID;
};

class C2 {
   public:
    C2() { cout << "C2()" << endl; }
    ~C2() { cout << "~C2()" << endl; }
};

class C3 {
   public:
    C3() : num(0), sub_obj1(123) { cout << "C3()" << endl; }
    C3(int n) : num(n), sub_obj1(123) { cout << "C3(int)" << endl; }
    C3(int n, int k) : num(n), sub_obj1(k) { cout << "C3(int, int)" << endl; }
    ~C3() { cout << "~C3()" << endl; }

   private:
    int num;
    C1 sub_obj1;
    C2 sub_obj2;
};

int main() {
    C3 a, b(1), c(2), d(3, 4);
    return 0;
}
```

`C1`、`C2`是`C3`的子对象，其中`C2`提供了缺省构造函数，故在`C3`中不用显式初始化；但`C1`只提供了一个带参数的构造函数，故必须在`C3`的 **初始化列表** 里面完成初始化。

输出
```
C1(int)
C2()
C3()
C1(int)
C2()
C3(int)
C1(int)
C2()
C3(int)
C1(int)
C2()
C3(int, int)
~C3()
~C2()
~C1()
~C3()
~C2()
~C1()
~C3()
~C2()
~C1()
~C3()
~C2()
~C1()
```

从输出可以看出构造链从内到外，而析构链从外向内。


## 移动构造函数 (C++ 11引入)

语法：`ClassName(ClassName&&);`

目的

* 用来偷“临时变量”中的资源（如内存）。

* 临时变量被编译器设置为常量形式，使用“拷贝构造”函数无法将资源“偷”出来（改动了元对象，违反常量的限制）。

* 基于 **右值引用** 定义的 **移动构造函数** 支持接受临时变量，能“偷”出临时变量中的资源。

```cpp
#include <iostream>

using namespace std;

class Test {
   public:
    int* buf;
    Test() {
        buf = new int(3);
        cout << "Test(): this->buf @ " << hex << buf << endl;
    }

    ~Test() {
        cout << "~Test(): this->buf @ " << hex << buf << endl;
        if (buf) delete buf;
    }

    Test(Test& t) : buf(new int(*t.buf)) {
        cout << "Test(const Test&) called. this->buf @ " << hex << buf << endl;
        t.buf = nullptr;
    }

    Test(Test&& t) : buf(t.buf) {
        cout << "Test(Test&&) called. this->buf @ " << hex << buf << endl;
        t.buf = nullptr;
    }
};

Test GetTemp() {
    Test tmp;
    cout << "GetTemp(): tmp.buf @ " << hex << tmp.buf << endl;
    return tmp;
}

void fun(Test t) { cout << "fun(Test t): t.buf @ " << hex << t.buf << endl; }

int main() {
    Test a = GetTemp();
    cout << "main() : a.buf @ " << hex << a.buf << endl;

    fun(a);

    return 0;
}
```

输出
```
Test(): this->buf @ 0x558bd1e13e70
GetTemp(): tmp.buf @ 0x558bd1e13e70
main() : a.buf @ 0x558bd1e13e70
Test(const Test&) called. this->buf @ 0x558bd1e142a0
fun(Test t): t.buf @ 0x558bd1e142a0
~Test(): this->buf @ 0x558bd1e142a0
~Test(): this->buf @ 0
```

在如上结果中没有调用移动构造函数，欲执行该函数，需要增加编译选项，禁止编译器进行返回值优化

```shell
g++ main.cpp --std=c++11 -fno-elide-constructors -o main
```

输出
```
Test(): this->buf @ 0x560b6f4dce70
GetTemp(): tmp.buf @ 0x560b6f4dce70
Test(Test&&) called. this->buf @ 0x560b6f4dce70
~Test(): this->buf @ 0
Test(Test&&) called. this->buf @ 0x560b6f4dce70
~Test(): this->buf @ 0
main() : a.buf @ 0x560b6f4dce70
Test(const Test&) called. this->buf @ 0x560b6f4dd2a0
fun(Test t): t.buf @ 0x560b6f4dd2a0
~Test(): this->buf @ 0x560b6f4dd2a0
~Test(): this->buf @ 0
```

可见，函数返回的时候调用的是移动构造函数。

如果将`Test`类中的移动构造函数去掉，同样禁用编译优化，则编译报错：
```
main.cpp: In function ‘int main()’:
main.cpp:38:21: error: cannot bind non-const lvalue reference of type ‘Test&’ to an rvalue of type ‘Test’
     Test a = GetTemp();
              ~~~~~~~^~
main.cpp:18:5: note:   initializing argument 1 of ‘Test::Test(Test&)’
     Test(Test& t) : buf(new int(*t.buf)) {
     ^~~~
```


## default修饰符 (C++ 11引入)

### 编译器自动生成的成员函数

如果以下成员函数用户都没有为类实现，则编译器会自动为类生成它们的缺省实现

* 默认构造函数 - 空函数，什么也不做

* 析构函数 - 空函数，什么也不做

* 拷贝构造函数 - 按bit位赋值对象所占内存内容

* 移动构造函数 - 与默认拷贝构造函数一样

* 赋值运算符重载 - 与默认拷贝构造函数一样

如果用户定义了上述某个成员函数，则编译器不再自动提供相应的默认实现。

### `=default`显式缺省

在默认函数定义或声明加上`=default`，可显式的只是编译器生成该函数的默认版本。

```cpp
class T {
   public:
    T() = default;
    T(int i) : data(i) {}

   private:
    int data;
};
```


## 继承

在已有类的基础上，可以通过“继承”来定义新的类，实现对已有代码的复用。

常见的继承方式：`public`, `private`

* `class Derived: [private] Base {...};` 缺省继承方式是`private`继承。

* `class Derived: public Base {...};`

基类/父类 - base class - 被继承的已有类

派生类/子类/扩展类 - derived class - 通过继承得到的新类

### 子类对象的构造与析构过程

基类中的数据成员通过继承成为子类对象的一部分，需要在构造子类对象的过程中调用积累的构造函数来初始化。

* 若没有显式调用，则编译器会自动生成一个对基类的默认构造函数的调用

* 若采用显式调用，则只能在子类构造函数的初始化列表中进行

先执行基类的构造函数来初始化继承来的数据，再执行子类的构造函数。

对象析构时，先执行子类的析构函数，再执行由编译器自动调用的基类的析构函数。

### 继承基类的构造函数

以如下代码为例

```cpp
#include <iostream>

using namespace std;

class Base {
   public:
    Base(int _data) : data(_data) { cout << "Base::Base(" << _data << ")\n"; }

   private:
    int data;
};

class Derive : public Base {
   public:
    using Base::Base;
    void print() { cout << "data = " << data << endl; }

   private:
    int data{2020};
};

int main() {
    Derive obj(356);
    obj.print();
    return 0;
}
```

`Derive`中使用`using Base::Base;`将`Base`中的所有构造函数都继承了过来。故可以调用带一个`int`型参数的构造函数。

```
Base::Base(356)
data = 2020
```

虽然基类构造函数的默认值不会被子类继承，但由默认参数导致的多个构造函数版本都会被子类继承。

如果基类的某个构造函数被声明成私有成员函数，则不能在子类中声明继承该构造函数。

如果子类使用了继承基类构造函数，编译器就不会再为子类生成默认构造函数。


## 函数重写 (override)

### 子类中的基类成员

子类对象包含从基类继承来的数据成员，它们构成了“基类子对象”。

基类中的私有成员，不允许在子类成员函数中被访问，也不允许子类的对象访问它们。

* 真正体现“基类私有”，对子类也不开放其权限

基类中的公有成员：

* 若使用`public`继承方式，则成为子类的公有成员，既可以在子类成员中访问，也可以被子类的对象访问；

* 若使用`private`继承方式，则只能供子类成员函数的访问，不能被子类对象访问。


考虑如下代码

```cpp
#include <iostream>

using namespace std;

class B {
   public:
    void f() { cout << "in B::f()..." << endl; }
};

class D1 : public B {};

class D2 : private B {
   public:
    void g() {
        cout << "in D2::g(), calling f()..." << endl;
        f();
    }
};

int main() {
    cout << "in main()..." << endl;

    D1 obj1;
    cout << "calling obj1.f()..." << endl;
    obj1.f();

    D2 obj2;
    cout << "calling obj2.g()..." << endl;
    obj2.g();

    return 0;
}
```

输出

```
in main()...
calling obj1.f()...
in B::f()...
calling obj2.g()...
in D2::g(), calling f()...
in B::f()...
```

如果私有继承的子类`D2`调用父类的共有函数，则会报错：

```
error: ‘B’ is not an accessible base of ‘D2
```

这里基类接口不许子类对象调用。


### 子类重写基类的成员函数

基类已定义的成员函数，在子类中可以重新定义，这被称为“函数重写”（override）

重写发生时，基类中该成员函数的其他重载函数都将被屏蔽掉，不能提供给子类对象使用。

可以在子类中通过`using 类名::成员函数名;`在子类中“=恢复”指定的基类成员函数（去掉屏蔽），使之重新可用。

考虑如下代码

```cpp
#include <iostream>

using namespace std;

class T {};

class B {
   public:
    void f() { cout << "B::f()\n"; }
    void f(int i) { cout << "B::f(" << i << ")\n"; }
    void f(double d) { cout << "B::f(" << d << ")\n"; }
    void f(T) { cout << "B::f(T)\n"; }
};

class D1 : public B {
   public:
    void f(int i) { cout << "D1::f(" << i << ")\n"; }
};

int main() {
    D1 d;
    d.f(10);
    d.f(4.9);
    // d.f();
    // d.f(T())
};
```

注意`d.f(4.9);`这一句编译会出警告，编译器执行强制类型转换使用整型参数的函数版本。
而被注释的两个语句则会出现编译错误，因为重写导致其他重载函数被屏蔽掉

输出

```
D1::f(10)
D1::f(4)
```

使用`using`恢复基类函数

```cpp
#include <iostream>

using namespace std;

class T {};

class B {
   public:
    void f() { cout << "B::f()\n"; }
    void f(int i) { cout << "B::f(" << i << ")\n"; }
    void f(double d) { cout << "B::f(" << d << ")\n"; }
    void f(T) { cout << "B::f(T)\n"; }
};

class D1 : public B {
   public:
    using B::f;
    void f(int i) { cout << "D1::f(" << i << ")\n"; }
};

int main() {
    D1 d;
    d.f(10);
    d.f(4.9);
    d.f();
    d.f(T());
    return 0;
};
```

输出

```
D1::f(10)
B::f(4.9)
B::f()
B::f(T)
```


## 虚函数

### 向上映射和向下映射

子类对象转换成基类对象，称为向上映射。而基类对象转换为子类对象，成为向下映射。

向上映射可以由编译器自动完成，是一种隐式的自动类型转换。

所有接受基类对象的地方（如函数参数），都可以使用子类对象，编译器会自动将子类对象转换为基类对象以便使用。

在如下代码中，子类重写了基类的`print`函数，将子类对象传给以基类作为形参的函数，子类被隐式转换为基类，故函数内调用的是基类的`print`函数。

```cpp
#include <iostream>

using namespace std;

class Base {
   public:
    void print() { cout << "Base::print()" << endl; }
};

class Derive : public Base {
   public:
    void print() { cout << "Derive::print()" << endl; }
};

void fun(Base obj) {
    obj.print();
}

int main() {
    Derive d;
    d.print();
    fun(d);
    return 0;
}
```

输出

```
Derive::print()
Base::print()
```

### 虚函数

对于被子类重写的成员函数，若它在基类中被声明为虚函数（如下所示），则通过积累指针或引用调用该函数成员时，编译器将根据所指（或引用）对象的实际类型决定是调用积累中的函数，还是调用子类重写的函数。

```cpp
class Base {
    public:
    virtual 返回类型 函数名(形参);
    ...
};
```

若某成员函数在基类中被声明为虚函数，当子类重写它时，无论是否声明为虚函数，该成员函数仍然是虚函数。

将上一节的例子中基类的`print`函数定义为虚函数，而函数`fun`的形参改为基类的引用类型，则调用的就是虚函数在子类中的实现。

```cpp
#include <iostream>

using namespace std;

class Base {
   public:
    virtual void print() { cout << "Base::print()" << endl; }
};

class Derive : public Base {
   public:
    void print() { cout << "Derive::print()" << endl; }
};

void fun(Base& obj) {
    obj.print();
}

int main() {
    Derive d;
    d.print();
    fun(d);
    return 0;
}
```

输出
```
Derive::print()
Derive::print()
```

### 虚析构函数

基类的析构函数总是要被声明成`virtual`的，这样才能保证子类定义的析构函数总能被执行。

事实上，最好的做法是：任何类的析构函数都应该被声明成`virtual`的，因为谁又能保证这个类不会被其他的类继承呢？

```cpp
#include <iostream>

using namespace std;

class B {
   public:
    virtual void show() { cout << "B.show()\n"; }
    virtual ~B() { cout << "~B()\n"; }
};

class D : public B {
   public:
    void show() { cout << "D.show()\n"; }
    ~D() { cout << "~D()\n"; }
};

void test(B* ptr) { ptr->show(); }

int main() {
    B* ptr = new D;
    test(ptr);
    delete ptr;

    return 0;
}
```

输出

```
D.show()
~D()
~B()
```

从输出可见析构函数的调用顺序是 **先调用子类的析构函数，再调用基类的析构函数** 。

如果删除基类析构函数前的`virtual`关键字，则输出为

```
D.show()
~B()
```

此时如果子类中独有的数据成员，则他们不会被释放，进而导致内存泄露。


### 禁止重写的虚函数`final` (c++ 11引入)

使用`final`管啊架子修饰的虚函数，子类不可对它进行重写 —— 改变函数的定义。

在派生过程中，`final`可以再继承关系链的`中途`进行设定，禁止后续子类对指定虚函数重写。

下属代码中，`class C`的实现是无法通过编译的。

```cpp
class A {
   public:
    virtual void fun() = 0;
};

class B : public A {
   public:
    void fun() final;
};

class C : public B {
   public:
    void fun();
};
```

`class A`中的`virtual void fun() = 0;`将`fun()`定义为一个 **纯虚函数** 。`A`由此成为一个 **抽象类** 。

C++中抽象类不能用于定义对象，这样的类一般用于 **定义接口** 。


## 自动类型转换

### 方法一 - 在源类中定义“目标类型转换运算符”

```cpp
#include <iostream>

using namespace std;

class Dst {
   public:
    Dst() { cout << "Dst::Dst()" << endl; }
};

class Src {
   public:
    Src() { cout << "Src::operator Dst() called" << endl; }

    operator Dst() const {
        cout << "Src::operator Dst() called" << endl;
        return Dst();
    }
};
```

### 方法二 - 在目标类中定义“源类对象做参数的构造函数”

```cpp
#include <iostream>

using namespace std;

class Src;

class Dst {
   public:
    Dst() { cout << "Dst::Dst()" << endl; }
    Dst(const Src& s) { cout << "Dst::Dst(const Src&)" << endl; }
};

class Src {
   public:
    Src() { cout << "Src::Src()" << endl; }
};
```

注： `class Src;`这一行是一个前置的类型声明，因为在`Dst`的定义中要用到`Src`类。

### 自动类型转换举例

测试代码如下（隐式转换）

```cpp
void Func(Dst d) {}

int main() {
    Src s;
    Dst d1(s);  // 这是直接构造，不是类型转换

    Dst d2 = s; // 自动类型转换，不是拷贝构造函数
    Func(s);    // 自动类型转换

    return 0；
}
```

注意：两种自定义类型转换的方法不能同时使用，只有在上述方法一和方法二使用且使用一个的前提下才能编译通过。


## 禁止自动类型转换

### 方法一 - `explicit`关键字

```cpp
#include <iostream>

using namespace std;

class Src;

class Dst {
   public:
    Dst() { cout << "Dst::Dst()" << endl; }

    explicit  // <1> 不准用于自动类型转换
    Dst(const Src& s) {
        cout << "Dst::Dst(const Src&)" << endl;
    }
};

class Src {
   public:
    Src() { cout << "Src::Src()" << endl; }

    explicit  // <2> 不准用于自动类型转换
    operator Dst() const {
        cout << "Src::operator Dst() called" << endl;
        return Dst();
    }
};
```

<1> - 该函数只用于构造函数，不用于自动类型转换（不能自动调用）

<2> - 该函数只用于类型转换，不用于自动类型转换（不能自动调用）

为此，如想让下方代码通过，上方代码中两处`explicit`必须保留且仅保留一处。

```cpp
void Func(Dst d) {}

int main() {
    Src s;
    Dst d1(s);

    Dst d2 = s;
    Func(s);

    return 0;
}
```

### 方法二 - `=delete`限定 (C++ 11 引入)

使用`=delete`修饰的成员函数，不允许被调用。

```cpp
#include <iostream>

using namespace std;

class T {
   public:
    T(int) {}
    T(char) = delete;   // 可消除自动类型转换带来的隐患，如没有` = delete`修饰符，则主函数中的语句都能编译通过。
};

void Fun(T t) {}

int main() {
    Fun(1);
    // Fun('X');    自动类型转换失败，编译不通过

    T ci(1);
    // T cc('X');   自动类型转换失败，编译不通过

    return 0;
}
```

* **`=delete`修饰一个函数** 和 **不写这个函数** 的区别:


## 强制类型转换（显式转换）

`dynamic_cast<Dst_type>(Src_var)` - 用于在类的集成体系中做转换

* `Src_var`必须是引用或指针类型，`Dst_Type`类中含有虚函数，否则会有编译错误

* 若目标类与原类之间没有及陈骨干西，转换失败，返回空指针（注：失败不导致运行崩溃）

`static_cast<Dst_Type>(Src_var)`

* 基类对象不能转换成子类对象，但基类指针可以转换成子类指针

* 子类对象（指针）可以转换成基类对象（指针）

* 没有继承关系的类之间，必须具有转换途径才能进行转换（自定义或者语言语法原生支持）

以如下代码为例

```cpp
#include <iostream>

using namespace std;

class B {
    public : virtual void f() {}
};

class D : public B {};

class E {};

int main() {
    D d1;
    B b1;
    
    // d1 = static_cast<D>(b1); /// Error: 从基类无法转换回子类
    b1 = static_cast<B>(d1);    /// OK: 可以从子类转换到基类
    // b1 - dynamic_cast<B>(d1);    /// ERROR: 被转换的必须是引用或指针

    B* pb1 = new B();
    D* pd1 = static_cast<D*>(pb1);
    if (pd1) {
        cout << "static_cast, B* --> D*: OK" << endl;
    }
    pd1 = dynamic_cast<D*>(pb1);
    if (pd1) {
        cout << "dynamic_cast, B* --> D*: OK" << endl;
    }

    D* pd2 = new D();
    B* pb2 = static_cast<B*>(pd2);
    if (pb2) {
        cout << "static_cast, D* --> B*: OK" << endl;
    }
    pb2 = dynamic_cast<B*>(pd2);
    if (pb2) {
        cout << "dynamic_cast, D* --> B*: OK" << endl;
    }

    E* pe = dynamic_cast<E*>(pb1);
    if (!pe) {
        cout << "dynamic_cast, B* --> E*: FAILED" << endl;
    }
    // pe = static_cast<E*>(pb1);   /// ERROR: 没有继承关系不能转换
    // E e = static_cast<E>(b1);    /// ERROR：没有提供转换途径

    return 0;
}
```

输出

```
static_cast, B* --> D*: OK
static_cast, D* --> B*: OK
dynamic_cast, D* --> B*: OK
dynamic_cast, B* --> E*: FAILED
```


## 函数模板

有些算法实现与类型无关，所以可以将函数的参数类型也定义为一种特殊的“参数”，这样就得到了“函数模板”。

定义函数模板的方法
```cpp
template <typename T>
返回类型 函数名(函数参数)；
```

例如，任意两个变量相加的“函数模板”
```cpp
template <typename T>
T sum(T a, T b) { return a + b; }
```

函数模板在调用时，因为编译器能自动推导出实际参数的类型，所以，形式上调用一个函数模板与普通函数没有区别，如

```cpp
int main() {
    int a = 3, b = 4;
    cout << sum(a, b);
    float c = 1.3, d = 1.9;
    cout << sum(c, d);
}
```

函数模板参数也可以赋默认值（缺省值），如

```cpp
#include <iostream>

using namespace std;

template<typename T0 = float, typename T1, typename T2 = float, typename T3, typename T4>
T0 func(T1 v1, T2 v2 = 0, T3 v3, T4 v4) {...}

func(1, 2, 3, 4);
func('a', 'b', "cde", 5);
```


## 类模板

在定义类时也可以将一些类型信息抽取出来，用模板参数来替换，从而使类更具有通用性。这种类被称为“类模板”。

```cpp
template <typename T>
class A {
   public:
    void print() { cout << data << endl; }

   private:
    T data;
};
```

类模板 --> 类 --> 对象

类模板的“模板参数”

* 类型参数：使用`typename`或`class`标记

* 非类型参数：整数，枚举，指针（指向对象或函数），引用（引用对象或引用函数）。其中整数类型是比较常用的，如
```cpp
template<typename T, unsigned size>
class Array {
    T elems[size];
    ...
};
Array <char, 10> array;
```

* 模板参数是另一个类模板，如下所示：
```cpp
template<typename T, template<typename TT0, typename TT1> class A>
struct Foo {
    A<T, T> bar;
};
```

## 成员函数模板

普通类中定义成员函数模板

```cpp
class NormalClass {
    public:
    int value;
    template<typename T> void set(T const& v) {
        value = int(v);
    }
    template<typename T> T get();
};

template<typename T>
T NormalClass::get() {
    return valuel;
}
```

类模板中定义成员函数模板

```cpp
template<typename T0>
class A {
    public:
    T0 value;
    template<typename T1> void set(T1 const& v) {
        value = T0(v);
    }
    template<typename T1> T1 get();
};

template<typename T0> template<typename T1>
T1 A::get() {
    return T1(value);
}
```

对于类模板外面定义的成员函数模板，会报编译错误
```
% g++ main.cpp -std=c++11 -o main

main.cpp:16:4: error: ‘template<class T0> class A’ used without template parameters
 T1 A::get() {
    ^
main.cpp:16:4: error: too many template-parameter-lists
```

## 模板特化

模板参数的具体化/特殊化

有时，有些类型并不适用，则需要对模板进行特殊化处理，这称为“模板特化”。

对于函数模板，如果有多个模板参数，则特化时必须提供所有参数的特例类型， **不能部分特化** 。

如 `char* Sum(char* char*);`

* 在函数名后用<>括号扩起具体类型
```cpp
template<>
char* Sum<char*>(char* a, char* b) {...}
```

* 由编译器推导出具体类型，函数名为普通形式
```cpp
template<>
char* Sum(char* a, char* b) {...}
```

### 模板的部分特化（偏特化）

* 对于类模板，允许部分特化，即部分限制模板的通用性，如：
```cpp
// 通用模板类
template<class T1, class T2> class A {...};
// 部分特化的模板类：第二个类型参数指定为 int
template<class T1> class A<T1m int> {...};
```

* 若指定所有类型，则<>内将为空
```cpp
tempalte<> class A<int, int> {...};
```

函数模板特化示例
```cpp
#include <bits/stdc++.h>

using namespace std;

template<typename T>
T Sum(T a, T b) {
    return a + b;
}

template<>
char* Sum(char* a, char* b) {
    char* p = new char[strlen(a), strlen(b) + 1];
    strcpy(p, a);
    strcpy(p + strlen(a), b);
    return p;
}

int main() {
    cout << Sum(3, 4) << ' ' << Sum(5.1, 13.8) << endl;

    char str1[] = "Hello, ", str2[] = "world";
    cout << Sum(str1, str2) << endl;

    return 0;
}
```

输出
```
7 18.9
Hello, world
```

类模板特化示例

```cpp
#include <bits/stdc++.h>

using namespace std;

template <typename T>
class Sum {
   public:
    Sum(T op1, T op2) : a(op1), b(op2) {}
    T DoIT() { return a + b; }

   private:
    T a, b;
};

template <>
class Sum<char*> {
   public:
    Sum(char* s1, char* s2) : str1(s1), str2(s2) {}
    char* DoIT() {
        char* tmp = new char[strlen(str1) + strlen(str2) + 1];
        strcpy(tmp, str1);
        strcat(tmp + strlen(str1), str2);
        return tmp;
    }

   private:
    char *str1, *str2;
};

int main() {
    Sum<int> obj1(3, 4);
    cout << obj1.DoIT() << endl;

    char s1[] = "Hello", s2[] = "THU";
    Sum<char*> obj2(s1, s2);
    cout << obj2.DoIT() << endl;

    return 0;
}
```

输出：
```
7
HelloTHU
```