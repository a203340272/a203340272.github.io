---
title: C++语法
date: 2026-11-17 12:00:00
tags:
  - C++
categories:
  - C++ 学习
published: true
---

# C++ 常用语法复习

> 按需查阅即可。示例默认使用 C++17；`std::` 是标准库命名空间。

## 1. 程序结构与输入输出

```cpp
#include <iostream>
#include <string>

int main() {
    std::string name;
    std::cin >> name;                 // 读取一个词
    std::cout << "Hello, " << name << '\\n';
    return 0;                         // 可省略：main 默认返回 0
}
```

- `#include <...>` 引入标准库头文件。
- `std::cout` 输出，`std::cin` 输入，`std::cerr` 输出错误信息。
- `\n` 换行；`std::endl` 还会刷新缓冲区，通常优先用 `\n`。
- `std::getline(std::cin, text)` 读取整行；若前面用过 `>>`，先处理遗留换行。
- `using namespace std;` 可以省略 `std::`，但大型项目通常避免放在头文件或全局作用域。

## 2. 变量、类型与常量

```cpp
int count = 3;
double price = 12.5;
char grade = 'A';
bool ready = true;
std::string title = "C++";
auto total = count + price;           // 编译器推导类型
const int limit = 100;                // 不可修改
constexpr int bufferSize = 256;       // 可在编译期求值
```

- 常用整数：`short`、`int`、`long long`；无符号版本写作 `unsigned int` 等。
- 浮点数：`float`、`double`；一般优先 `double`。
- `auto` 保留初始化表达式推导出的类型；不要用它隐藏难以理解的类型。
- `const` 表示不可通过该名字修改；`constexpr` 要求值可在编译期确定。
- 使用 `static_cast<T>(value)` 做明确类型转换：`double x = static_cast<double>(count);`。
- 整数除法会截断小数部分：`5 / 2 == 2`；有小数结果可写 `5.0 / 2`。

## 3. 运算符与条件

```cpp
int a = 7, b = 3;
int sum = a + b, remainder = a % b;
bool inRange = (a >= 0 && a < 10);
int bigger = (a > b) ? a : b;         // 条件运算符
```

- 算术：`+ - * / %`；比较：`== != < <= > >=`。
- 逻辑：`&&`（且）、`||`（或）、`!`（非）。短路求值：左侧已足以决定结果时，右侧不再执行。
- 赋值：`= += -= *= /= ++ --`。注意 `=` 是赋值，`==` 才是比较。

## 4. 分支与循环

```cpp
if (score >= 90) {
    std::cout << "A\\n";
} else if (score >= 60) {
    std::cout << "及格\\n";
} else {
    std::cout << "未及格\\n";
}

switch (choice) {
case 1:
    std::cout << "一\\n";
    break;                            // 防止继续执行后续 case
case 2:
    std::cout << "二\\n";
    break;
default:
    std::cout << "其他\\n";
}

for (int i = 0; i < 5; ++i) std::cout << i << ' ';
while (count > 0) --count;
do { ++count; } while (count < 3);    // 至少执行一次
```

- `break` 结束当前循环或 `switch`；`continue` 跳过本轮循环剩余部分。
- 循环边界要确认是 `< n` 还是 `<= n`，避免越界。

## 5. 函数、参数与返回值

```cpp
int add(int a, int b) {                // 值传递：复制参数
    return a + b;
}

void setZero(int& value) {             // 引用传递：可修改调用者变量
    value = 0;
}

void print(const std::string& text) {  // 常量引用：避免复制且不允许修改
    std::cout << text << '\\n';
}

int power(int base, int exponent = 2) { // 默认参数
    int result = 1;
    for (int i = 0; i < exponent; ++i) result *= base;
    return result;
}
```

- 函数声明（原型）可以放在定义之前：`int add(int a, int b);`。
- 参数传值会复制；大对象通常用 `const T&`；需要修改实参时用 `T&`。
- 函数可以重载：同名函数参数列表不同；仅返回类型不同不能构成重载。
- 默认参数通常从最右侧参数开始连续提供。

## 6. 字符串、数组与常用容器

```cpp
#include <array>
#include <vector>
#include <string>

std::string word = "hello";
word += "!";
std::cout << word.size() << ' ' << word[0];

int raw[3] = {10, 20, 30};            // 固定长度数组
std::array<int, 3> fixed{10, 20, 30}; // 推荐的固定长度数组
std::vector<int> values{1, 2, 3};     // 可变长度数组
values.push_back(4);
values.pop_back();
for (int value : values) std::cout << value << ' ';
for (int& value : values) value *= 2; // 引用元素以便修改
```

- `std::vector<T>`：动态数组，常用 `size()`、`empty()`、`push_back()`、`pop_back()`、`at(i)`。
- `v[i]` 不检查边界；`v.at(i)` 越界时会抛出异常。
- 范围 `for`：`for (const auto& item : values)` 可避免复制且只读；要修改元素用 `auto&`。
- `std::string` 常用 `size()`、`empty()`、`find()`、`substr()`、`push_back()`。

## 7. STL常用标准库容器与算法

```cpp
#include <algorithm>
#include <map>
#include <set>
#include <unordered_map>

std::sort(values.begin(), values.end());
auto it = std::find(values.begin(), values.end(), 3);

std::map<std::string, int> scores;      // 有序键值表
scores["Lin"] = 95;

std::unordered_map<std::string, int> counts; // 哈希键值表
counts["apple"]++;

std::set<int> uniqueValues{3, 1, 3};    // 自动去重且有序
```

- `std::sort(first, last)` 默认升序；降序：`std::sort(v.begin(), v.end(), std::greater<>());`（需 `<functional>`）。
- `std::map` 按键排序；`std::unordered_map` 通常不保证遍历顺序。
- `std::set` 保存唯一元素；`std::unordered_set` 是无序哈希集合（需 `<unordered_set>`）。
- 判断 `find` 是否找到：`it != values.end()`。
- 其他常用容器：`std::queue` 队列、`std::stack` 栈、`std::deque` 双端队列；分别需包含对应头文件。

## 8. 指针、引用与内存

```cpp
int value = 10;
int* ptr = &value;                     // 指针保存地址
*ptr = 20;                             // 解引用，修改指向的对象
int& ref = value;                      // 引用是对象的别名

#include <memory>
auto owned = std::make_unique<int>(42); // 独占所有权
std::cout << *owned << '\\n';
```

- `&value` 在表达式中取地址；声明中的 `int& ref` 表示引用。
- 指针可以为空：`int* ptr = nullptr;`；解引用前必须确认它指向有效对象。
- 引用必须在初始化时绑定对象，之后不能改绑。
- 优先使用局部对象、容器和智能指针管理资源；避免手动 `new` / `delete`。
- `std::unique_ptr<T>` 表示独占所有权；需要共享所有权时才考虑 `std::shared_ptr<T>`。

## 9. 结构体与类

```cpp
struct Point {
    double x = 0;
    double y = 0;
};

class Account {
public:
    explicit Account(int initial) : balance(initial) {} // 构造函数、成员初始化列表

    void deposit(int amount) {
        if (amount > 0) balance += amount;
    }

    int getBalance() const { return balance; } // const 成员函数不修改对象状态

private:
    int balance;
};

Account account(100);
account.deposit(25);
```

- `struct` 默认成员公开；`class` 默认成员私有。两者都可以有成员函数、构造函数和访问控制。
- `public` 可从外部访问；`private` 仅类内部可访问；`protected` 还允许派生类访问。
- 构造函数在对象创建时初始化对象；成员初始化列表写在 `:` 后。
- `explicit` 可阻止不必要的隐式转换。
- 对象成员用 `.` 访问；对象指针用 `->` 访问。
- 继承写法：`class Derived : public Base { ... };`。需要运行时多态时，基类接口通常使用 `virtual`，并用 `override` 标记派生类重写。

## 10. 枚举、别名与模板

```cpp
using Id = unsigned long long;

enum class Color { red, green, blue };
Color color = Color::green;

template <typename T>
T maximum(T a, T b) {
    return (a > b) ? a : b;
}
```

- 优先使用 `enum class`：枚举值有作用域，且不会随意隐式转换成整数。
- `using NewName = ExistingType;` 为类型创建别名。
- 模板让函数或类适用于多种类型；调用时常能自动推导模板参数。

## 11. 异常与文件

```cpp
#include <fstream>
#include <stdexcept>

try {
    std::ifstream file("data.txt");
    if (!file) throw std::runtime_error("无法打开文件");

    std::string line;
    while (std::getline(file, line)) std::cout << line << '\\n';
} catch (const std::exception& error) {
    std::cerr << error.what() << '\\n';
}
```

- `throw` 抛出异常；`try` 包住可能失败的代码；`catch` 处理异常。
- `std::ifstream` 读文件，`std::ofstream` 写文件，`std::fstream` 可读写。
- 文件流离开作用域时会自动关闭；先检查流状态再读写。

## 12. Lambda 与现代 C++ 常用写法

```cpp
auto square = [](int x) { return x * x; };
std::sort(values.begin(), values.end(),
          [](int a, int b) { return a > b; }); // 降序

int offset = 5;
auto addOffset = [offset](int x) { return x + offset; }; // 按值捕获
```

- Lambda 基本形式：`[捕获](参数) { 函数体 }`。
- `[x]` 按值捕获；`[&x]` 按引用捕获；`[=]` 默认按值；`[&]` 默认按引用。
- 引用捕获时，确保被捕获对象的生命周期长于 Lambda 的使用时间。
- `auto` 可保存 Lambda；不同 Lambda 表达式有不同的匿名类型。

## 13. 头文件与编译

```cpp
// main.cpp
#include <iostream>

int main() {
    std::cout << "C++\\n";
}
```

```bash
g++ -std=c++17 -Wall -Wextra -Wpedantic main.cpp -o main
```

- 每个源文件通常以 `.cpp` 结尾；头文件常用 `.h` 或 `.hpp`。
- 头文件声明接口，`.cpp` 文件实现函数；避免在多个源文件中重复定义同一个非 `inline` 函数。
- 编译时指定标准（如 `-std=c++17`）并启用警告（如 `-Wall -Wextra`），有助于发现问题。
