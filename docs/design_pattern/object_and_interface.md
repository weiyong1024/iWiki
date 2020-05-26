# 对象与接口

## 从FOP到OOP

FOP - Functional oriented programming  
OOP - Object oriented programming

目标：引出对象  
思考：如何确定对象边界、封装什么、暴露什么、隐藏什么

### 以”电子计价器为例“

实现一个电子计价器，根据多个商品的单价和重量计算总价。

FOP版本
```cpp
#include <iostream>

using namespace std;

int main() {
    float apple_price = 5.5;
    float banana_price = 3.4;
    float apple_weight = 0.0;
    float banana_weight = 0.0;
    float total = 0.0;

    cout << "Weight of apple: " << endl;
    cin >> apple_weight;
    cout << "Weight of banana: " << endl;
    cin >> banana_weight;
    total = apple_price * apple_weight + banana_price * banana_weight;
    cout << "Payment: " << total << endl;
}
```

### 变化分层

对于每次都要变化的“重量”，“单价”是相对不变的。

为区分两种不同层次的“变化”，“单价”应该被封在计价器里

OOP版本
```cpp
#include <iostream>

using namespace std;

class Calculator {
   public:
    Calculator(float _apple_price, float _banana_price)
        : apple_price(_apple_price), banana_price(_banana_price) {}

    float CalTotal(float apple_weight, float banana_weight);

   private:
    float apple_price, banana_price;
};

float Calculator::CalTotal(float apple_weight, float banana_weight) {
    return apple_price * apple_weight + banana_price * banana_weight;
}

int main() {
    Calculator c(5.5, 3.4);

    float apple_weight = 0.0;
    float banana_weight = 0.0;

    cout << "Weight of apple: " << endl;
    cin >> apple_weight;
    cout << "Weight of banana: " << endl;
    cin >> banana_weight;
    cout << "Payment: " << c.CalTotal(apple_weight, banana_weight) << endl;
 }
```

### 封装

把不常变化的`apple_price`和`banana_price`封装起来，形成`Calculator`概念，使用`Calculator`时把经常变化的重量作为参数。

* 接口：类暴露出来的部分，是类所提供的功能，隐藏实现细节

UML类图

| Calculator                                                        |
|:------------------------------------------------------------------|
| - apple_price: float<br> - banana_price: float                    |
| + CalTotal(in apple_weight: float, in banana_weight:float): float |


## 如何抽象出对象

### 问题

$A, B, C, D$ 四个人，其中一个人是 PyTorch 的作者，对此四个人分别作了陈述：

* $A$ : 不是我

* $B$ : 作者是 $C$

* $C$ : 作者是 $D$

* $D$ : $C$ 说得不对　

已知其中有三句真话、一句假话，问作者是谁？

### FOP版本

```cpp
#include <iostream>

using namespace std;

int main() {
    for (char author = 'A'; author <= 'D'; author++) {
        int count = 0;
        count += (author != 'A');
        count += (author == 'C');
        count += (author == 'D');
        count += (author != 'D');

        if (count == 3) {
            cout << "The author could be " << author << endl;
            break;
        }
    }
    return 0;
}
```

### OOP版本

| 问题中可变的部分        | 描述方法                      |
| -------------------- | ----------------             |
| 候选人数量　　          | 整型参数 $n$                  |
| 真实陈述的数量          | 整型参数　$correct$           |
| 每个候选人的陈述的真实性 | 一组与候选人对应的返回布尔型的函数 |

注意到，对于返回每个候选人的陈述真实性的函数，函数的内部逻辑依赖于陈述本身。另一方面，陈述同意可以表述为作者　是/不是　“某个人”的形式。

可以重载`()`运算符来实现。

```cpp
#include <iostream>

using namespace std;

class Candidate {
   public:
    Candidate(bool _equal, char _author) : equal(_equal), author(_author) {}
    bool operator()(char suspect);

   private:
    char author;
    bool equal;
};

bool Candidate::operator()(char suspect) {
    return equal ? (suspect == author) : (suspect != author);
}

char solve(int number, int correct, Candidate* candidates) {
    for (int i = 0; i < number; i++) {
        int count = 0;
        char suspect = 'A' + i;

        for (int j = 0; j < number; j++) count += candidates[j](suspect);

        cout << count << endl;

        if (count == correct) return suspect;
    }

    return '\0';
}

int main() {
    Candidate candidates[] = {Candidate(false, 'A'), Candidate(true, 'C'),
                              Candidate(true, 'D'), Candidate(false, 'D')};
    char suspect = solve(4, 3, candidates);

    if (suspect != '\0') {
        cout << "The author is " << suspect << endl;
    } else {
        cout << "Nooooooooooooooop!" << endl;
    }
}
```

基于OOP版本的实现，问题可以很容易地扩展到 $n$ 个候选人，且有 $m$ 个候选人的陈述真实的情况。

## 如何定义接口

### 问题：旋转矩阵

设计一个类，对于给定整数 $N$ ，输出旋转矩阵，形式如下：

$$
1, 16, 15, 14, 13\\
2, 17, 24, 23, 12\\
3, 18, 25, 22, 11\\
4, 19, 20, 21, 10\\
5,  6,  7,  8,  9
$$

### 设计思路：自顶向下

这个类如何被使用？

```cpp
Matrix obj(size);
obj.fill();
cout << obj;
```

根据使用方法设计`Matrix`的接口

```cpp
class matrix {
   public:
    Matrix(int size);
    void fill();
    friend ostream& operator<< (ostream& out, const Matrix& m);
};
```

实现类的接口

确定需要哪些成员变量

```cpp
class Matrix {
   public:
    Matrix(int size);
    ~Matrix();
    void fill();
    friend ostream& operator<< (ostream& out, const Matrix& m);
   private:
    int size_;
    int *data_;
};
```

实现构造函数、析构函数和输出流操作符

```cpp
Matrix::Matrix(int size) : size_(size) {
    data_ = new int[size * size];
    memset(data_, 0, sizeof(int) * size_ * size_);
}

Matrix::~Matrix() {
    delete[] data_;
}

ostream& operator<< (ostream& out, const Matrix& m) {
    for (int r = 0; r < m.size_; r++) {
        for (int c = 0; c < m.size_; c++)
            cout << *(m.data_ + r * m.size_ + c) << '\t';
        cout << endl;
    }
}
```

实现填充函数

增加一个辅助函数计算并填充

```cpp
class Matrix {
   public:
    ...
   private:
    ...
    int FindPosition();
};

void Matrix::fill() {
    for (int num = 1; num <= size_ * size_; num++) {
        int pos = FindPosition();
        data_[pos] = num;
    }
}
```

剩下的任务就是实现`FindPosition()`。

对于不同的矩阵形式，也只需改变`FindPosition`即可。


## 多态的应用

程序设计的 **开闭原则** ，程序对变化的需求应该是open的，但应该尽量不改动原有代码

这时可以将`FindPostion`在`Matrix`定义为纯虚函数。

### 模板方法

抽象类（基类）定义算法不变的骨架。

算法的需要改变的细节由实现类（子类）以重写（override）的实现。

在使用时，调用抽象类的算法骨架方法，再由这个方法根据需要调用累的算法细节实现。

### 针对接口而不是针对实现

* 通过抽象出“抽象概念”，设计出描述这个抽象概念的“抽象类”，或称为“接口类”，这个类有一系列（纯）虚函数，描述了这个类的“接口”

* 对这个接口类进行继承并实现这些（纯）虚函数，从而形成这个抽象概念的“实现类” —— 实现可以有很多种以适应变化

* 在使用这个概念的时候，我们使用接口类来引用这个概念，而不直接使用实现类，从而避免实现类的改变造成整个程序的大规模修改

