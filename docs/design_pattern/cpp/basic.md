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
    int operator() (int a, int b) {
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
