# noexcept

`noexcept` 是 C++11 引入的关键字，用于指示函数是否可能抛出异常。

具体地说，`noexcept` 关键字有两种用法：

1. **函数说明符**：`noexcept` 可以用作函数的说明符，放在函数声明或者定义的尾部。例如：

   ```
   void myFunction() noexcept;
   ```

   这表示函数 `myFunction` 不会抛出任何异常。

2. **运算符**：`noexcept` 也可以用于运算符的右侧，表示对表达式进行 noexcept 操作符的评估。例如：

   ```
   bool canThrow = noexcept(myFunction());
   ```

   这将在编译时评估 `myFunction()` 是否可能抛出异常，并将结果赋值给 `canThrow`。

使用 `noexcept` 的好处在于可以帮助编译器进行更好的优化，特别是在处理异常的成本较高的情况下。如果编译器知道一个函数不会抛出异常，它可以生成更加高效的代码。此外，`noexcept` 还允许程序员更清晰地表达他们的意图，从而提高代码的可读性

# 默认实现析构函数

```c++
~HikFrame() =default
```

# explicit

`explicit` 是 C++ 中的一个关键字，通常用于修饰单参数的构造函数，用来防止隐式类型转换。具体来说，`explicit` 关键字的作用有以下几点：

1. **禁止隐式类型转换**：当一个构造函数被 `explicit` 修饰时，它就不能被用于隐式类型转换，而只能用于显式地创建对象。这样可以避免一些意外的行为和潜在的错误。

   ```
   class MyClass {
   public:
       explicit MyClass(int value) { // 使用 explicit 修饰的构造函数
           // 构造函数的定义
       }
   };
   
   int main() {
       MyClass obj = 10; // 错误，不能进行隐式类型转换
       MyClass obj2(10); // 正确，显式创建对象
       return 0;
   }
   ```

2. **提高代码的清晰度**：使用 `explicit` 可以使代码更加明确和易于理解。当某个类的构造函数被标记为 `explicit` 时，读者可以清楚地知道对象是如何被创建的，不会产生歧义。

3. **防止意外的类型转换**：有时候隐式类型转换可能会导致意外的行为或错误。通过使用 `explicit`，可以避免这种情况的发生，提高代码的健壮性。

总的来说，`explicit` 关键字主要用于构造函数，用来明确指定只能通过显式调用来创建对象，从而减少代码中的潜在问题并提高可读性。

# [[nodiscard]]

当一个函数的返回值被 `[[nodiscard]]` 修饰时，调用者如果不使用返回值，编译器会发出警告。以下是一个简单的例子：

```
[[nodiscard]] int add(int a, int b) {
    return a + b;
}

int main() {
    add(1, 2); // 调用函数但未使用返回值，编译器会发出警告
    return 0;
}
```

编译时可能会得到类似以下的警告信息：

```
warning: ignoring return value of function declared with 'nodiscard' attribute [-Wunused-result]
```

在这个例子中，`add` 函数返回两个整数的和，但是在 `main` 函数中调用 `add` 函数时未使用返回值，因此编译器会发出警告提示。如果确实不需要使用返回值，可以通过显式地将返回值赋值给一个临时变量或者将函数调用强制转换为 `void` 来消除警告：

```
int main() {
    int result = add(1, 2); // 将返回值赋值给一个临时变量，消除警告
    static_cast<void>(add(1, 2)); // 将函数调用强制转换为 void，消除警告
    return 0;
}
```

这样做可以提醒开发者注意处理函数的返回值，避免因为忽略返回值而导致的潜在问题。

# static constexpr

 用于声明静态的编译期常量，这样的常量在运行时是不可修改的，并且在编译期就已经确定了其值。这种常量在性能和可读性上都有很好的优势，因为它们在编译期就被解析，而不需要在运行时进行计算。

# stoi

stoi(tokens[i]) 是 C++ 中的一个函数调用，用于将字符串 tokens[i] 转换为整数。stoi 函数会尝试将字符串解析为整数，并返回相应的整数值。如果字符串无法解析为整数，则会引发 std::invalid_argument 异常或 std::out_of_range 异常，具体取决于输入是否在有效范围内。

# reinterpret_cast
reinterpret_cast 是 C++ 中的一种强制类型转换运算符，用于将一个指针或引用转换为另一种不同类型的指针或引用,如将一种指针类型强制转换为另一种完全不同的指针类型。

特性：
不执行检查：reinterpret_cast 不会进行任何类型安全检查，这意味着在编译时不会检查转换是否合理。它只是简单地改变类型视图，不会修改数据本身的二进制表示。

用于指针、引用和整数类型：常见用法是将指针转换为其他类型的指针，或将指针转换为整数（如 uintptr_t），或将整数转换为指针。

不改变数据的内存布局：虽然类型发生了变化，但数据的内存布局保持不变。只是编译器对该数据的解释方式发生了变化。

使用场景：
指针类型转换：如将 int* 转换为 char*，或将某种类的指针转换为 void* 或其他类的指针。这种转换往往不安全，可能导致未定义行为。

类型擦除：有时候，可能需要将指针类型转换为某种通用的表示方式，如 void* 或整型，以便存储或传递。然后可以使用 reinterpret_cast 恢复原始类型。

操作硬件或内存映射：在嵌入式开发中，通常需要将特定的内存地址转换为某种类型的指针来访问硬件寄存器。
```

#include <iostream>
using namespace std;

int main() {
    int a = 65;
    char* p = reinterpret_cast<char*>(&a);  // 将 int* 转换为 char*
    
    cout << "a = " << a << endl;  // 输出：a = 65
    cout << "p = " << *p << endl;  // 输出：p = A，因为 65 在 ASCII 表中对应 'A'

    return 0;
}
```
这个用法并不会改变a的内存内容，只是转换指向它的指针。
在这个例子中，reinterpret_cast 将指向 int 的指针转换为指向 char 的指针。虽然 a 是一个整数，但当我们通过 char* 访问它时，编译器会将它解释为字符。
使用风险：reinterpret_cast 可以轻松绕过类型系统的保护，导致潜在的危险行为，因此在使用时应格外小心，确保你理解内存布局和类型之间的关系。
未定义行为：如果你用 reinterpret_cast 将某些数据强制转换为不兼容的类型并访问它们，可能会导致未定义行为，特别是在跨平台开发时。
