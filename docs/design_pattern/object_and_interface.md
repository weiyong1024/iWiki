# 找到对象，确定接口

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

