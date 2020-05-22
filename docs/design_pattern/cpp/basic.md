# C++基础

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