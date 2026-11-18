---
title: Effective C++ 条款 1-5
date: 2026-10-16 12:00:00
tags:
  - C++
  - Effective C++
categories:
  - C++ 学习
  - Effective C++
published: true
---

# Effective C++ 条款 1—5

> 学习目标：每条先用自己的话说清楚，再用一个小实验验证；不要只背原文。

## 条款 1：视 C++ 为一个联邦

核心理解： C++ 由 C、Object-Oriented C++、Template C++ 和 STL 等部分组成。不同部分有不同的规则与最佳实践，因此高效编程守则会随使用场景变化。
==c++高效编程守则视状况而变化，取决于你使用c++的哪一个部分。==
不要用同一套思维解决所有问题。例如，使用 `std::vector` 时，通常不必再手动管理动态数组的内存；使用类时，则要认真考虑对象的初始化和生命周期。

验证方向：

1. C 风格数组：理解固定大小、下标访问和边界风险。
    
2. 类与对象：理解构造函数、成员初始化列表和封装。
    
3. 模板函数：理解类型参数、类型推导和泛型编程。
    
4. STL 容器：理解动态容器、迭代器和标准算法。

四种方式的对比：

|比较维度|C 风格数组|面向对象|模板|STL|
|---|---|---|---|---|
|主要对象|固定大小的数组|类和对象|类型参数化的代码|容器与算法|
|初始化|`int a[3] = {1,2,3};`|构造函数|由实参推导模板类型|`vector<int> v = {1,2,3};`|
|大小管理|固定|由类的设计决定|由模板类型和实现决定|由容器提供相应机制|
|主要关注点|下标、边界、内存|封装、生命周期、对象行为|类型推导、实例化|迭代器、算法、容器行为|
|典型工具|数组、指针|类、继承、多态|`template`|`vector`、`sort`|

## 条款 2：尽量以 const、enum、inline(模板函数) 替换 # define

我的理解：能用有类型、有作用域的 C++ 写法表达常量或小函数时，就不要用没有类型检查的宏。

==对于单纯常量，最好以const对象或enums替换#defines==

==对于形似函数的宏，最好改用inline函数替换#defines==

验证方向：把 `#define BUFFER_SIZE 64` 改成 `constexpr int buffer_size = 64;`，观察编译器能否检查类型和作用域。

## 条款 3：尽可能使用 const

我的理解：宁可以编译器替换处理器，凡是不会被修改的变量、参数、返回值或成员函数，都应该用 `const` 表达这个承诺，让编译器和读代码的人都能及时发现误修改。

核心知识点：

- 修饰普通变量、修饰指针

- 修饰函数参数：对于不需要修改的输入参数，使用 `const`，避免意外修改调用方的数据。

- 修饰函数返回值：合理使用 `const` 限制返回结果的修改，尤其要注意返回引用或指针时的含义。

- 修饰成员函数：在成员函数末尾添加 `const`，表示该函数不会通过普通成员访问修改对象的状态，并允许 `const` 对象调用它。

- 修饰引用：使用 `const T&` 传递较大的只读对象，避免不必要的复制。

- 注意 `const` 的边界：`const` 不一定意味着对象在物理上绝对不变，也不代表指针指向的数据一定不可修改，需要结合具体类型判断。

==将某些东西声明为 `const` 可帮助编译器侦测出错误用法。`const` 可被施加于任何作用域内的对象、函数参数、函数返回类型，以及成员函数本体。==

==编译器强制实施 bitwise constness（位常量性），但你编写程序时应该使用 conceptual constness（概念常量性）==

==当 `const` 和 `non-const` 成员函数有着实质等价的实现时，令 `non-const` 版本调用 `const` 版本可避免代码重复。但要注意，这种做法需要谨慎使用 `const_cast` 和 `static_cast`，避免在真正的常量对象上执行修改操作。==

验证方向：

1. 取消下面代码的注释，确认编译器拒绝修改常量。

2. 分别尝试修改 `const int*`、`int* const` 和 `const int* const`，理解 `const` 修饰的位置。

3. 定义一个类，为成员函数分别添加和移除末尾的 `const`，验证 `const` 对象只能调用符合要求的成员函数。

4. 将函数参数从 `T` 改为 `const T&`，观察是否避免对象复制，以及函数内部能否修改参数。

5. 尝试通过 `const_cast` 修改真正声明为 `const` 的对象，理解去除 `const` 限定不代表修改操作就合法。

```cpp
#include <iostream>
#include <string>

class TextBlock {
public:
    explicit TextBlock(std::string text)
        : text_(std::move(text)) {}

    // const 成员函数：只读访问
    const char& operator[](std::size_t position) const {
        return text_[position];
    }

    // 非 const 成员函数：允许修改
    char& operator[](std::size_t position) {
        return text_[position];
    }

private:
    std::string text_;
};

int main() {
    const int limit = 3;
    // limit = 4;  // 编译错误

    const TextBlock text("Hello");
    char ch = text[0];  // 正常：调用 const 版本
    // text[0] = 'Y';   // 编译错误：不能修改 const 对象

    TextBlock mutableText("Hello");
    mutableText[0] = 'Y';  // 正常：调用非 const 版本

    std::cout << ch << '\n';               // H
    std::cout << mutableText[0] << '\n';   // Y
}
```

补充说明：示例中的 `std::move` 需要包含 `<utility>` 头文件，因此完整代码还应添加 `#include <utility>`。

## 条款 4：确定对象被使用前已经初始化

我的理解：对象应该在声明时就得到明确的初始值，避免读取未初始化数据导致随机结果；类成员还要按声明顺序初始化。C++ 中，不同的初始化方式、对象类型和存储位置会影响初始化行为。不要依赖未初始化的内置类型变量的值，也不要混淆赋值与初始化。

核心知识点：

- 构造函数使用成员初始化列表、成员按照它们在类中声明的顺序初始化。

- 成员初始化顺序：成员按照它们在类中声明的顺序初始化，而不是按照成员初始化列表的书写顺序。

- 不同初始化方式的区别：静态存储期对象通常会先进行零初始化；局部内置类型变量若没有初始化，其值不能安全地读取。

- 避免跨编译单元的初始化顺序问题：不同源文件中的全局或命名空间作用域对象，其动态初始化顺序可能造成依赖问题。可以考虑使用函数内局部静态对象等方式解决。

- 使用标准库类型管理资源：优先使用 `std::string`、`std::vector`、智能指针等类型，减少手动管理资源和遗漏初始化的风险。

## 请记住

1. 为内置型对象进行手工初始化，因为 C++ 不保证初始化它们。

2. 构造函数最好使用成员初始化列表，而不要在构造函数本体内使用赋值操作。初始化列表列出的成员变量，其排列顺序应该和它们在 class 中的声明顺序相同。

3. 为免除“跨编译单元之初始化次序”问题，请以 local static 对象替换 non-local static 对象。

验证方向：

1. 比较 `int value{};` 和未初始化的 `int value，理解为什么这是错误做法。

2. 分别使用构造函数初始化列表和构造函数体内赋值，观察两者的区别。

3. 定义多个类成员，让初始化列表的书写顺序与成员声明顺序不同，观察实际初始化顺序。

4. 编写两个依赖对方全局对象初始化结果的源文件，理解跨编译单元初始化顺序风险。

验证方向：

```cpp
1.

int a;  // 未初始化 int a{};
std::cout << a << '\n';  // 错误做法

int a = 0;
std::cout << a << '\n';  // 输出 0
2.
class Example {
public:
    // 方式一：成员初始化列表
    explicit Example(const std::string& name)
        : name_(name) {
        std::cout << "初始化列表\n";
    }

    // 如果写成下面这样，则是构造函数体内赋值：
    // explicit Example(const std::string& name) {
    //     name_ = name;
    // }

private:
    std::string name_;
};

int main() {
    Example e("Alice");
}
3.
int getValue(const char* name, int value) {
    std::cout << "初始化 " << name << '\n';
    return value;
}

class Example {
public:
    Example()
        : second_(getValue("second_", 2)),
          first_(getValue("first_", 1)) {}

private:
    int first_;
    int second_;
};

int main() {
    Example e;
}
4.
#include <iostream>

// B.cpp 中定义的全局变量
extern int b;

// 全局对象初始化时依赖 b
int a = b + 1;

// 提供访问接口
void PrintA()
{
    std::cout << "a = " << a << '\n';
}

#include <iostream>

// A.cpp 中定义的全局变量
extern int a;

// 全局对象初始化时依赖 a
int b = a + 1;

// 提供访问接口
void PrintB()
{
    std::cout << "b = " << b << '\n';
}

#include <iostream>

extern int a;
extern int b;

void PrintA();
void PrintB();

int main()
{
    std::cout << "a = " << a << '\n';
    std::cout << "b = " << b << '\n';

    PrintA();
    PrintB();

    return 0;
}

关键问题：不同编译单元之间的动态初始化顺序没有可靠保证。
可以将全局对象改成函数内的局部静态对象。
如果两个对象确实存在依赖关系，应该明确依赖方向，避免相互依赖。
// A.cpp
int GetA()
{
    static int a = 10;
    return a;
}
// B.cpp
int GetA();

int GetB()
{
    static int b = GetA() + 1;
    return b;
}
```

## 条款 5：了解 C++ 默默编写并调用哪些函数

我的理解：如果没有自己声明，编译器可能生成默认构造、析构、复制构造和复制赋值等特殊成员函数，所以类是否能安全复制取决于成员资源的语义。比如引用是不可以被拷贝赋值的.

验证方向：用编译器生成的复制构造和复制赋值复制一个只含整数的对象，再给类加入指针资源，观察为什么需要自己设计复制或禁用复制：

```cpp
#include <string>

template<class T>
class NamedObject {
public:
    NamedObject(std::string& name, const T& value)
        : nameValue(name), objectValue(value) {}

private:
    std::string& nameValue;  // 引用成员
    const T objectValue;     // const 成员
};

int main() {
    std::string newDog("Persephone");
    std::string oldDog("Satch");

    NamedObject<int> p(newDog, 2);//
    NamedObject<int> s(oldDog, 36);

    p = s;  // 编译错误


NamedObject<int> p1(newDog, 2); // ① 调用普通构造函数

NamedObject<int> p2(p1);        // ② 调用拷贝构造函数
NamedObject<int> p3 = p1;        // ③ 调用拷贝构造函数

p2 = p1;                         // ④ 调用拷贝赋值运算符 错误

}
```

## 完成记录

| 条款  | 我是否能脱稿解释 | 实验结果 |
| ---:|:--------:| ---- |
| 1   | [ ]      |      |
| 2   | [ ]      |      |
| 3   | [ ]      |      |
| 4   | [ ]      |      |
| 5   | [ ]      |      |

**下一步：** 打开本文件，先补写条款 1 的一句话，再运行对应验证方向。
