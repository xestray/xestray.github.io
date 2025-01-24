---
    comments: true
    statistics: true
---

# C++ 笔记

!!! quote "写在前面的话"
    虽然之前已经在一定程度上了解并使用 C++ 了，但还未系统地学习过，这个页面主要用于记录我在学习 C++ 过程中的遇见的值得记忆和有意思的知识点（至少我是这么认为的）。

    笔记主要来自于我在学习 [www.learncpp.com](https://www.learncpp.com/) 和《C++ primer》过程中的一些总结，同时也会参考一些其他来自互联网的资料。

    - 笔记的初衷是在我本人遗忘某个知识点时可以快速地查阅，因此可能会有一些我认为是常识的内容没有记录在这里。
    - 写笔记的过程就是不停往里加新东西，因此显得比较杂乱无章，也许将来某一天会把这些知识点系统性地整理一遍（会，会吗？）。

## 变量

### 初始化

C++中有 5 种常见的初始化形式：
```CPP
int a;         // default-initialization     (no initializer)

// Traditional initialization forms:
int b = 5;     // copy-initialization        (initial value after equals sign)
int c ( 6 );   // direct-initialization      (initial value in parenthesis)

// Modern initialization forms (preferred):
int d { 7 };   // direct-list-initialization (initial value in braces)
int e {};      // value-initialization       (empty braces)
```
在第 4 种初始化方法中，`{}`被称为初始化器，它可以防止窄化（narrowing）的发生，是一种更加安全的初始化方法。
```CPP
int x = 5.3;   // x = 5
int y { 5.3 }; // error: narrowing conversion of '5.3' from 'double' to 'int' inside { } [-Wnarrowing]
```
除此之外，列表初始化也可以写成`int x = {5};`的形式，称为复制列表初始化（copy list initialization），但这种方法比较少见。

!!! note "何时使用`{ 0 }`和`{}`进行初始化？"
    - 当我们希望初始化一个变量的值为 0 时，应该使用`{ 0 }`，如

        ```CPP
        int x { 0 };
        std::cout << x;
        ```

    - 当变量的初始化值是临时存在的，并且将要被替换时，应该使用`{}`，如

        ```CPP
        int x {};
        std::cin >> x;
        ```

!!! note "未被使用的初始化变量"
    当我们初始化一个变量但未使用时，编译器会发出警告

    ```cpp
    int main() {
        int x { 5 };
        return 0;
    }
    ```

    一般而言我们可以通过直接删除掉或注释掉这个变量的声明来消除警告，但如果我们只是当前用不到这个变量，在将来可能会用到，我们可以通过在变量名前加上`[[maybe_unused]]`来消除警告。

    ```CPP
    [[maybe_unused]] int x { 5 };
    ```

    - 此外，编译器在编译后可能会优化程序中的这些变量，因此通常来说它们不会影响性能。

### constexpr

编译器在编译时可能会对程序中一些常量进行优化，例如

```cpp
const int x { 5 };
std::cout << x;
```

就可能会被编译器直接优化为

```cpp
std::cout << 5;
```

要实现上面这个程序在编译层面的**优化**，就需要编译器在**编译时**就得知这个变量是一个常量，并且知道这个常量的值是多少。但是对于上面的这个例子而言，编译器可能优化，也可能不优化，因为它通常并不能确定能否实现**编译时优化**（compile-time optimization）。

如果我们希望编译器总能在编译时优化这个常量，我们可以使用 `constexpr` 关键字提示编译器这个变量是一个在编译时就可以确定值的常量，格式为

```cpp
constexpr int x { expr };
```

其中 `expr` 是一个在编译时就可计算出结果的表达式，称为常量表达式（constant expression）。

!!! info "常量表达式"
    通常而言，常量表达式包含以下内容：（较常见的内容）

    - 字面值常量（literal constants）

        例如 `5`、`3.14`、`'a'`、`"hello"` 等

    - 以常量表达式作为操作数的运算符

        例如 `5 + 3`、`2 * sizeof(int)` 等

    - `constexpr` 变量

    - 被常量表达式初始化的常量整形变量

        例如 `constexpr int x { 5 };`，这是一个来自于历史原因的例外——在现代 C++ 中，constexpr variable 是更优选

    - constexpr function calls with constant expression arguments 

        调用常量表达式作为参数的 constexpr 函数

    以下内容不能作为常量表达式被使用

    - Non-const variables 
    
        非常量变量

    - 操作数不是常量表达式的运算符
    - Const non-integral variables 
    
        非整型的常量变量（即使它是被一个 constexpr variable 初始化的）

    - 调用 non-constexpr 函数的函数（即使函数的返回值是常量表达式）
    - 函数的参数（即使是 constexpr 函数）
    - 运算符 `new`，`delete`，`throw`，`typeid`，`operator,`（逗号运算符）

    包含以上任意一个的表达式都是运行时表达式（runtime expression）。

!!! note "const vs. constexpr"
    - `const` 意味着变量的值在被初始化后不可改变。initializer 的值可以在编译时已知或运行时已知，这个变量在运行时才可被计算出来。
    - `constexpr` 意味个变量可以在常量表达式中使用，它的值在编译时就已知，initializer 的值必须在编译时就已知，这个变量可在编译时或运行时被计算出来。

!!! extra
    一个常量表达式可以在编译时求值，但编译器不一定会这么做。编译器可能会选择在运行时求值，这样可以减少编译时间。

    - **编译器只在需要常量表达式时才被要求在编译时计算常量表达式的值**，也就是说，在不刚需常量表达式的语句中，编译器可以选择在编译时还是在运行时计算常量表达式的值。

    例如

    ```cpp
    const int x { 3 + 4 };
    int y { 3 + 4 }; 
    ```

    上面的 `x` 是一个常量变量，它的初始值必须在编译时就确定，因此编译器一定会在编译时就计算 `3 + 4` 的值。而 `y` 是一个非常量变量，编译器可以选择在编译时计算 `3 + 4` 的值，也可以选择在运行时计算。

### constexpr 函数

constexpr 函数是可以在常量表达式中调用的函数。当 constexpr 函数需要在编译时就计算出结果时（例如它的返回值是一个 constexpr variable 的 initializer），它必须在编译时就运行；否则，它可以在程序编译时或运行时运行。

```cpp
#include <iostream>

int max(int x, int y) // this is a non-constexpr function
{
    if (x > y)
        return x;
    else
        return y;
}

constexpr int cmax(int x, int y) // this is a constexpr function
{
    if (x > y)
        return x;
    else
        return y;
}

int main()
{
    int m1 { max(5, 6) };            // ok
    const int m2 { max(5, 6) };      // ok
    constexpr int m3 { max(5, 6) };  // compile error: max(5, 6) not a constant expression

    int m1 { cmax(5, 6) };           // ok: may evaluate at compile-time or runtime
    const int m2 { cmax(5, 6) };     // ok: may evaluate at compile-time or runtime
    constexpr int m3 { cmax(5, 6) }; // okay: must evaluate at compile-time

    return 0;
}
```

### 多文件程序与全局变量

- 非 const 的全局变量被认为是 `extern` 的，即它可以被其他文件访问。如果我们在一个文件中定义了一个全局变量，而在另一个文件中使用这个全局变量，我们需要在使用的文件中前向声明这个全局变量。
- const 的全局变量会被默认是 `static` 的，即它只能被当前的文件访问和使用。如果我们希望在另一个文件中使用这个 const 全局变量，我们需要在定义的文件中使用 `extern` 关键字来定义这个 const 全局变量，同时在使用它的文件中前向声明这个 const 全局变量。

```cpp title="main.cpp"
#include <iostream>

extern int g_x;       // this extern is a forward declaration of a variable named g_x that is defined somewhere else
extern const int g_y; // this extern is a forward declaration of a const variable named g_y that is defined somewhere else

int main()
{
    std::cout << g_x << ' ' << g_y << '\n'; // prints 2 3

    return 0;
}
```

```cpp title="a.cpp"
// global variable definitions
int g_x { 2 };              // non-constant globals have external linkage by default
extern const int g_y { 3 }; // this extern gives g_y external linkage
```

!!! abstract 
    ```cpp
    // Global variable forward declarations (extern w/ no initializer):
    extern int g_y;                 // forward declaration for non-constant global variable
    extern const int g_y;           // forward declaration for const global variable
    extern constexpr int g_y;       // not allowed: constexpr variables can't be forward declared

    // External global variable definitions (no extern)
    int g_x;                        // defines non-initialized external global variable (zero initialized by default)
    int g_x { 1 };                  // defines initialized external global variable

    // External const global variable definitions (extern w/ initializer)
    extern const int g_x { 2 };     // defines initialized const external global variable
    extern constexpr int g_x { 3 }; // defines initialized constexpr external global variable
    ```

## std::string

传统的 C 风格字符串是一个字符数组，以空字符 `'\0'` 结尾，它在一些情况下使用并不方便（例如不能通过赋值来为字符串分配新的值）。为此 C++17 引入了两种额外的字符串类型：`std::string`（需要 `#include <string>`） 和 `std::string_view`需要 `#include <string_view>`）。它们并不是 fundamental type 而是 class type，因此它们有一些额外的功能。

`std::string` 可以通过赋值语句来更新字符串的值，也可以储存不同大小的字符串

```cpp
std::string name1 { "Chihaya" };
name1 = "Nagasaki";
```

在上面的例子中，我们使用长度为 8 的 C 风格字符串 `"Chihaya"` 初始化了 `name1`（包括空字符 `'\0'`），但 `std::string` 也可以使用其他的 `std::string` 对象来初始化或赋值

!!! info 
    如果 `std::string` 没有足够的内存来存储字符串，它将使用动态内存分配来请求额外的内存空间存储字符串。这意味着 `std::string` 可以存储任意长度的字符串，只要系统内存足够，但也意味着 `std::string` 的操作可能会比 C 风格字符串慢。

### 输入字符串

当我们直接使用 `std::cin` 来输入字符串时，它会在遇到空白字符（空格、制表符、换行符等）时停止输入，因此我们无法输入包含空白字符的字符串。

对于这种情况，我们可以使用 `std::getline()` 来读取整行的输入。`std::getline()` 需要两个参数：第一个是 `std::cin`，第二个是一个 `std::string` 对象，它会读取整行的输入并存储到这个 `std::string` 对象中。

```cpp
std::string name{};
std::getline(std::cin >> std::ws, name);
```

上面这个程序会把整行的输入 `Nagasaki Soyo` 都存储到 `name` 中，而如果直接用 `std::cin` 的话，只会存储 `Nagasaki`。  

!!! question "`std::ws` 是啥"
    `std::ws` 是一个操纵符（manipulator），它会告诉 `std::cin` 忽略输入流中的前导空白字符（空格、制表符、换行符等）。
    
    在上面的例子中，我们使用 `std::ws` 来忽略 `std::cin` 中的前导空白字符，这样可以保证 `std::getline()` 不会读取到有效信息之前的空白字符，直到遇到换行符为止才会停止读取。

### std::string 的长度

`std::string` 是一个类类型，我们可以直接调用它的成员函数 `length()` 来获取字符串的长度，比如 

```cpp
std::string name { "Chihaya" };
name.length(); // 7
```

尽管 `std::string` 要求以 null 字符结尾（从 C++11 开始），但 `length()` 并不会把 null 终止字符计算在内。

!!! tip
    `std::string::length()` 返回的是一个无符号整数值（很可能是 `size_t`），因此如果我们想要把字符串的长度分配给一个 `int` 变量，最好使用 `static_cast` 来避免编译器出现有关有符号/无符号转换警告。

!!! extra "获取字符串长度的其他方法"

    在 C++20 中，我们还可以使用 `std::ssize()` 函数来用一个大的有符号整数来获取 `std::string` 的长度（通常为 `std::ptrdiff_t`）

    由于 `std::ptrdiff_t` 可能大于 `int`，因此如果我们要用一个 `int` 变量来保存长度，也要使用 `static_cast` 来避免警告。

    ```cpp
    int len { static_cast<int>(std::ssize(name)) };
    ```

### 函数与 std::string

由于 `std::string` 的初始化成本很高，当我们把 `std::string` 作为函数的参数时，我们应该尽量避免使用按值传递，因为这会导致函数参数的拷贝初始化，在大部分情况下，我们应该使用引用传递或是改用 `std::string_view` 作为参数。

类似地，在大多数情况下，我们也应该避免在函数中按值返回 `std::string`，因为这同样会产生一个昂贵的副本。

### Literals for std::string

默认情况下，`"Hello"` 会被认为是一个 C 风格字符串，它的类型是 `const char[6]`，是一个字面量常量（literal constant）。

如果我们想要创建一个 `std::string` 类型的字面量，我们可以在双引号后添加一个 `s` 后缀，其中 `s` 必须小写，例如 `"Hello"s`。

- `s` 后缀在命名空间 `std::literals::string_literals` 中定义，我们可以使用 `using namespace std::literals::string_literals;` 来使用它。因为这个命名空间中的定义几乎不会和任何其他代码发生冲突，因此我们直接 `using` 是安全的。

!!! note 
    `"Hello"s` 会被解析为 `std::string { "Hello", 5 }`，它创建一个临时的使用 C 风格字符串字面量来初始化的 `std::string` 对象（长度为 5，不包括隐式 null 终止符）。 

## std::string_view

我们上面提到过 `std::string` 进行初始化（或复制）的开销很大，例如下面的例子中，为了输出一个字符串，我们不得不制作了 `"Hello, world!"` 的两个副本：一个是在 `main()` 中初始化 `std::string` 对象 `s`，另一个是在 `printString()` 中初始化参数 `str`。

```cpp
#include <iostream>
#include <string>

void printString(std::string str) // str makes a copy of its initializer
{
    std::cout << str << '\n';
}

int main()
{
    std::string s{ "Hello, world!" }; // s makes a copy of its initializer
    printString(s);

    return 0;
}
```

为了避免初始化带来的开销，我们可以使用 `std::string_view` 来对一个**现有**字符串（C 风格字符串、`std::string` 对象或另一个 `std::string_view` 对象）的只读访问——可以访问和使用字符串的内容，但不能修改它。

!!! tip "如何理解 `std::string_view`"
    “view” 的含义可以理解为它只是一个指向现有字符串的“视图”——比起直接制作一个完全相同的物体，我们拍一张照片带走以便于后续的观看显然更方便也更经济。

在下面的例子中我们在函数的参数声明中使用 `std::string_view` 来代替 `std::string`，从而避免了初始化 `std::string` 带来的开销。

```cpp
#include <iostream>
#include <string>
#include <string_view>

void printSV(std::string_view str)
{
    std::cout << str << '\n';
}

int main()
{
    printSV("Hello, world!"); // call with C-style string literal

    std::string s2{ "Hello, world!" };
    printSV(s2); // call with std::string

    std::string_view s3 { s2 };
    printSV(s3); // call with std::string_view

    return 0;
}
```

我们还可以从上面的例子中得知一些关于 `std::string_view` 的特点：

- `std::string_view` 可以用许多不同类型的字符串进行初始化，包括C 风格字符串、`std::string` 对象或另一个 `std::string_view` 对象。
- 当函数使用 `std::string_view` 作为参数时，我们可以直接传递 C 风格字符串、`std::string` 对象或另一个 `std::string_view` 对象，在传参过程中 C 风格字符串和 `std::string` 将会被隐式转换为 `std::string_view`。

### 赋值

对 `std::string_view` 对象的赋值只会改变它查看的字符串，而不会改变原先查看字符串的任何内容。

```cpp
std::string name { "Alex" };
std::string_view sv { name };
std::cout << sv << '\n';    // prints Alex

sv = "John";
std::cout << sv << '\n';    // prints John

std::cout << name << '\n';  // prints Alex
```

上面的例子中，`sv = "John"` 会使 `sv` 转向查看字符串 `"John"`，但不会改变 `name` 的值。

### Literals for std::string_view

`std::string_view` 的字面量后缀是 `sv`，例如 `"Hello"sv`。它的定义在命名空间 `std::literals::string_view_literals` 中，我们也可以直接使用 `using namespace std::literals::string_view_literals;` 来使用它，原因和 `std::string` 的字面量后缀一样。

### viewer

`std::string_view` 在它的整个生命周期中都依赖于它所查看的字符串的存在，如果这个字符串被销毁了，那么 `std::string_view` 将会指向一个无效的内存地址，这会导致未定义行为。

!!! note "dangling view"
    当 `std::string_view` 指向一个已经被销毁的字符串时，我们称这个 `std::string_view` 为悬空视图（dangling view）。

    - 可以和悬空指针类比

    例如在下面几种情况中，`sv` 都会成为一个悬空视图：

    === "case 1"

        ```cpp title="case 1"
        int main()
        {
            std::string_view sv{};

            {
                std::string s{ "Hello, world!" };
                sv = s;
            } // s is destroyed here, so sv is now viewing an invalid string

            std::cout << sv << '\n'; // undefined behavior

            return 0;
        }
        ```

        在第一种情况中，我们使用局部变量 `s` 初始化了 `sv`，但当 `s` 超出作用域时，它会被销毁，因此 `sv` 将会成为一个悬空视图。

    === "case 2"

        ```cpp title="case 2"
        std::string getName()
        {
            std::string s { "Alex" };
            return s;
        }

        int main()
        {
        std::string_view name { getName() }; // name initialized with return value of function
        std::cout << name << '\n'; // undefined behavior

        return 0;
        }
        ```

        在第二种情况中，函数的返回值会存储在一个临时变量中，这个临时变量会被用于初始化 `name`，但这个初始化语句结束后，这个临时变量就会被销毁，因此 `name` 将会成为一个悬空视图。

    === "case 3"

        ```cpp title="case 3"
        using namespace std::string_literals;
        std::string_view name { "Alex"s }; // "Alex"s creates a temporary std::string
        std::cout << name << '\n'; // undefined behavior
        ```

        这种情况比较特别：`"Alex"s` 会创建一个临时的 `std::string` 对象，这个对象会被用于初始化 `name`，但这个初始化语句结束后，这个临时变量就会被销毁， `name` 就成为一个悬空视图了。

!!! warning    
    - 不要使用 `std::string` 字面量来初始化一个 `std::string_view`。
    - 使用 C 风格字符串字面量或者 `std::string_view` 字面量来初始化 `std::string_view` 是安全的，因为它们在整个程序的生命周期中都是有效的。
    - 只要我们能够保证 C 风格字符串对象、`std::string` 对象或者 `std::string_view` 对象在 `std::string_view` 的整个生命周期中都是有效的，那么我们就可以使用它们来初始化 `std::string_view`。

当 `std::string_view` 所指向的字符串对象被修改时，会导致未定义的行为

```cpp
std::string s { "Hello, world!" };
std::string_view sv { s }; // sv is now viewing s

s = "Hello, a!";    // modifies s, which invalidates sv (s is still valid)
std::cout << sv << '\n';   // undefined behavior
```

上面的例子中，可能出现两种情况：

- 当 `s` 重新分配内存以存储新的字符串数据时，它会把原先的内存释放掉，这会导致 `sv` 指向的内存地址无效
- 当 `s` 仍然使用原先的内存地址来存储新的字符串数据时，`s` 会用新的字符串数据覆盖原先的字符串数据，`std::string_view` 会查看新的字符串

    - 如果新字符串比原先的字符串长，那么 `std::string_view` 查看到的是新字符串的子串
    - 如果新字符串比原先的字符串短，那么 `std::string_view` 查看到的是新字符串加上一些随机的垃圾字符

!!! tip "`std::string_view` 未必以 `'\0'` 结尾"
    由于 `std::string_view` 可以查看一个字符串的子串，因此它的结尾不一定是 `'\0'`，这意味着在绝大多数情况下我们不能把 `std::string_view` 当作 C 风格字符串来使用。当我们需要一个以 `'\0'` 结尾的字符串时，我们应该使用 `std::string`。

!!! abstract
    - `std::string`

        - 初始化和复制 `std::string` 的成本很高，应尽量避免
        - 尽可能避免按值传递 `std::string`，应该使用引用传递或 `std::string_view`
        - 对 `std::string` 的任何修改都会使得对应的 `std::string_view` 称为悬空视图
        - 可以按值返回一个本地的 `std::string` 对象

    - `std::string_view`

        - `std::string_view` 是一个只读的字符串视图
        - 由于 C 风格字符串在整个程序的生命周期中都是有效的，因此总可以使用 C 风格字符串来初始化 `std::string_view`
        - 使用一个已经无效了的（dangling） `std::string_view` 会导致未定义行为

---

## 类型

### 类型转换

C++ 标准定义了将不同的基本类型（以及某些情况下的复合类型）转换为其他类型的规则，称为标准类型转换（standard type conversions）。标准转换可大致分为 4 类，

- Numeric promotions
- Numeric conversions
- Arithmetic conversions
- Other conversions

#### Numeric promotions

numeric promotion 指的是将*较小（或称较窄）*的整数类型转换为*较大（或称较宽）*的整数类型，在转换的过程中不会出现数据的丢失。

numeric promotion 包括两大类：

1. 浮点数提升（floating-point promotion）

    将 `float` 类型转换为 `double` 类型

2. 整型提升（integer promotion）

    常见的整型提升包括

    - 有符号的 `char` 或有符号的 `short` 转换为 `int`
    - 如果 `int` 能保存 `unsigned char`、`char8_t`、`unsigned short` 的整个范围，那么就转换为 `int`，否则转换为 `unsigned int`
    - `bool` 可以被转换为 `int`，其中 `true` 转换为 `1`，`false` 转换为 `0`

!!! tip
    一些类型转换并不被认为是 numeric promotion，而被认为是 numeric conversion，例如 `char` 到 `short`，因为 `int` 到 `long` 在大多数系统中都是相同大小的。

#### Numeric conversions

数值转换（numeric conversion）有五种基本类型

1. 将一种整型数据转换为任何其他的整型数据（不包括整型提升）

    ```cpp
    short s = 3;        // convert int to short
    long l = 3;         // convert int to long
    char ch = s;        // convert short to char
    unsigned int u = 3; // convert int to unsigned int
    ```

2. 将一种浮点类型数据转换为任意其他的浮点类型（不包括浮点提升）

    ```cpp
    float f = 3.0;        // convert double to float
    long double ld = 3.0; // convert double to long double
    ```

3. 将一种浮点类型转换为任意整型

    ```cpp
    int i = 3.14; // convert double to int
    ```

4. 将一种整型转换为浮点类型

    ```cpp
    double d = 3; // convert int to double
    ```

5. 将整型或浮点类型转换为 bool

    ```cpp
    bool b1 = 3;    // convert int to bool
    bool b2 = 3.14; // convert double to bool
    ```

#### Arithmetic conversions

以下运算符要求它的操作数具有相同的类型，当操作数的类型不同时，会发生隐式类型转换，称为算术转换（arithmetic conversion）。

- 二元算数运算符 `+`、`-`、`*`、`/`、`%`
- 二元关系运算符 `==`、`!=`、`<`、`>`、`<=`、`>=`
- 二进制按位算术运算符 `&`、`|`、`^`
- 条件运算符 `? :`

    条件运算符的第一个参数会被认为是布尔类型，因此不要求它与后两个参数类型相同。

!!! note 
    重载运算符不受通常的算术转换规则的约束。

算术转换的规则较为复杂，因此我们这里只介绍化简过后的规则。

编译器会对各种类型进行一个排名

- long double
- double
- float
- long long
- long
- int
- short

它会参考以下的规则来决定如何进行算术转换

1. 将两种类型进行简单转换

    - 如果一个是整型另一个是浮点型，那么整型将会被转换为浮点型
    - 如果两种类型同为整型或同为浮点型，那么会先进行数值提升（numeric promotion）

2. 将两种类型变为相同的类型

    - 如果此时两种类型一个是有符号的，另一个是无符号的，则参考下面的特殊规则
    - 否则，排名较低的操作数将会转换为排名较高的操作数的类型

!!! note "无符号数与有符号数的转换"
    - 如果无符号操作数的排名大于等于有符号操作数的排名，那么有符号操作数将会被转换为无符号操作数的类型。
    - 如果有符号操作数的类型可以表示无符号操作数类型中的所有值，则无符号操作数将会被转换为有符号操作数的类型。
    - 否则，两个操作数都转换为有符号操作数相应的无符号类型。

### 类型别名

在 C 中，我们使用 `typedef` 来为一个类型定义一个别名，而在 C++ 中，我们可以使用 `using` 关键字来定义类型别名。

```cpp
typedef int score;
using score = int;
```

这两种方法是等价的，但 `using` 更好用的地方在于我们不仅不容易出现在使用 `typedef` 时类型和类型的新名字的顺序相反的错误（`typedef score int;`），而且对于一些复杂的类型别名，`using` 更加直观。

比如下面的例子中，如果我们使用 `typedef` 时，就会得到一个相当丑陋并且难以理解的别名，而使用 `using` 就直观多了

```cpp
typedef int (*FcnType)(double, char); // FcnType hard to find
using FcnType = int(*)(double, char); // FcnType easier to find
```

### 类型推导

类型推导（type deduction，type inference）是指编译器根据变量的初始化表达式来推导变量的类型。我们可以是同 `auto` 关键字进行类型推导。

```cpp
auto d { 5.0 };   // 5.0 is a double literal, so d will be deduced as a double
auto i { 1 + 2 }; // 1 + 2 evaluates to an int, so i will be deduced as an int
auto x { i };     // i is an int, so x will be deduced as an int
auto y { 3.5f };  // 3.5f is a float literal, so y will be deduced as a float
```

因为函数调用也可用于变量的初始化，所以我们也可以使用 `auto` 来推导变量类型为函数的返回类型（函数返回类型不能为 `void`）。

```cpp
int add(int x, int y) {
    return x + y;
}

int main() {
    auto sum { add(5, 6) }; // add() returns an int, so sum's type will be deduced as an int
}
```

#### 类型推导会删除 const

在大多数情况下，类型推导会删除（drop）推导类型中的 `const` 。例如：

```cpp
const int a { 5 }; // a has type const int
auto b { a };      // b has type int (const dropped)
```

如果我们想让 `b` 也是 `const int` 类型，我们可以使用 `const auto` 

```cpp
const auto b { a }; // b has type const int (const dropped but reapplied)
```

由于 `constexpr` 并不是类型系统的一部分，因此它不能进行类型推导。但值得一提的是 `constexpr` 是隐式的 `const`，也同样会在推导时把 `const` 删除。

```cpp
constexpr double a { 3.4 };  // a has type const double (constexpr not part of type, const is implicit)

auto b { a };                // b has type double (const dropped)
const auto c { a };          // c has type const double (const dropped but reapplied)
constexpr auto d { a };      // d has type const double (const dropped but implicitly reapplied by constexpr)
```

---

## 函数

### 函数重载

我们把两个或多个函数的名称相同，但是参数列表不同的函数称为**函数重载**（function overloading）。

当对已重载的函数进行调用时，编译器将尝试根据函数调用中使用的参数将函数调用与适当的重载函数相匹配，这称为**重载解析**（overload resolution）。

!!! tip
    编译器可以通过**参数的数量**、**参数的类型**进行重载解析，但不能通过**函数返回值类型**进行重载解析。

对于按值传递的参数，编译器不会考虑 const 限定符，因此下面这两个函数不能被编译器所区分

```cpp
void print(int);
void print(const int); // not differentiated from print(int)
```

!!! info "函数的类型签名"
    函数的类型签名（function signature）是指函数头中用于区分不同函数的部分，在 C++ 中，这包括函数名称、参数数量、参数类型和函数级限定符，不包括函数返回值类型。函数的类型签名可用于重载解析。

#### 函数重载解析与模糊匹配

当调用一个已被重载的函数时，编译器将逐步执行一系列规则可，以寻找一个最佳的匹配。

1. 编译器将尝试寻找最佳匹配。

    - 首先，编译器将查看是否存在一个参数列表与函数调用中的参数列表完全一致的重载函数，如果有，那么这个函数就是最佳匹配，编译器将调用这个函数。
    - 接着，编译器将尝试采用一些简单转换（trivial conversions）来匹配参数，包括：

        - 左值到右值的转换
        - 限定符的转换（例如非 const 到 const）
        - 非引用到引用的转换

2. 如果没有找到最佳匹配，编译器将会尝试通过对参数应用 numeric promotion 来寻找匹配的重载函数。
3. 如果 numeric promotion 也没有找到匹配的重载函数，编译器则会尝试 numeric conversion。
4. 如果 numeric conversion 也没能让编译器找到匹配项，那么编译器将尝试通过用户定义的类型转换（user-defined type conversion）来寻找匹配的重载函数。

    ```cpp
    class X 
    {
    public:
        operator int() { return 0; } // Here's a user-defined conversion from X to int
    };

    void foo(int) {}
    void foo(double) {}

    int main()
    {
        X x; 
        foo(x); // x is converted to type int using the user-defined conversion from X to int
        return 0;
    }
    ```

    在上例中，编译器首先发现没有能和 `foo(X)` 完全匹配的重载函数，接着它依次尝试数值提升和数值转换，但都没有找到匹配的重载函数。最后编译器找到了一个用户定义的类型转换，它将 `X` 转换为 `int`，因此 `foo(int)` 就成为了最佳匹配。

    - 如果用户定义的类型转换是 `X` 转换到 `char` 的，那么编译器会先把它转换为 `char`，然后把 `char` 提升为 `int`。

5. 如果通过用户定义的转换也没能找到匹配项，编译器就会尝试查找使用省略号（`...`）的重载函数。
6. 如果此时还未找到匹配项，编译器将放弃并报告无法找到匹配函数的错误。

!!! property "函数重载解析"
    在上面的每一个步骤中，编译器都会尝试对函数调用中的参数应用一大堆不同的类型转换，每次转换后，编译器都会检查是否有重载函数与之匹配，其结果有 3 种：

    - 没有找到匹配的函数。编译器按顺序移动到下一步。
    - 找到了一个最佳匹配，至此重载解析结束，后续步骤不再执行。
    - 找到了多个可能匹配的函数，编译器将会报告一个模糊匹配（或称不明确匹配，ambiguous match）的编译错误。

#### ambiguous match

当编译器在同一步骤中发现多个可以匹配的函数时，编译器将停止匹配并发出编译错误，指出它发现了不明确的函数调用。

!!! example "ambiguous match"
    === "example 1"
        ```cpp title="ambiguous match 1"
        void foo(int) {}
        void foo(double) {}

        int main()
        {
            foo(5L); // 5L is type long

            return 0;
        }
        ```

        编译器在发现找不到 `foo(long)` 之后会尝试 numeric promotion，但 `long` 不能提升（暂时不考虑 `long long`），因此编译器会尝试数值转换。

        但在检查所有数值转换规则的过程中，编译器发现 `foo(int)` 和 `foo(double)` 都可以通过数值转换匹配 `foo(5L)`，因此这里的函数调用被认为是模糊的。

    === "example 2"
        ```cpp title="ambiguous match 2"
        void foo(unsigned int) {}
        void foo(float) {}

        int main()
        {
            foo(0);       // int can be numerically converted to unsigned int or to float
            foo(3.14159); // double can be numerically converted to unsigned int or to float

            return 0;
        }
        ```

        尽管我们可能主观地认为 `foo(0)` 应该调用 `foo(unsigned int)`，而 `foo(3.14159)` 应该调用 `foo(float)`，但编译器并不会这样认为。因为 `0` 是一个 `int` 类型的字面量，它可以被转换为 `unsigned int`或`float` ；而 `3.14159` 是一个 `double` 类型的字面量，它也可以被转换为 `unsigned int` 或 `float`。因此两次调用都是不明确的。

!!! extra "多个参数的重载函数匹配"
    如果有多个参数，编译器将依次将匹配规则应用于每个参数。最终选择的函数至少有一个参数比其他所有函数都更好，并且剩余参数至少不会比其他函数更差。

    ```cpp title="example 1"
    void print(char, int) {
        std::cout << 'a' << '\n';
    }

    void print(char, double) {
        std::cout << 'b' << '\n';
    }

    int main() {
        print('x', 'a');

        return 0;
    }
    ```

    在上例中两个函数都与第一个参数完全匹配，但是第一个 `print()` 可通过数值提升来匹配第二个参数，第二个 `print()` 需要通过数值转换才能匹配第二个参数，因此第一个 `print(char, int)` 是最佳匹配。

    ```cpp title="example 2"
    void print(int, float) {
        std::cout << '1' << '\n';
    }

    void print(char, double) {
        std::cout << '2' << '\n';
    }

    int main()
    {
        print(1, 1.0);

        return 0;
    }
    ```

    在这个例子中 `print(1, 1.0);` 与 `print(int, float)` 的第一个参数完全匹配，但第二个参数需要进行数值转换；同时与 `print(char, double)` 的第二个参数完全匹配，但第一个参数需要进行数值转换。
    
    因此这里不满足“至少有一个参数比其他所有函数都更好，并且剩余参数至少不会比其他函数更差”的条件，因此编译器认为这是一个不明确的函数调用。

### 删除函数

有时候我们在调用函数时不希望它通过类型转换匹配到了一个不能按预期运行的函数，例如

```cpp
void printInt(int x) {
    std::cout << x << '\n';
}

int main()
{
    printInt(5);    // okay: prints 5
    printInt('a');  // prints 97 -- make no sense
    printInt(true); // print 1   -- make no sense

    return 0;
}
```

对于 `printInt('a')` 和 `printInt(true)` 而言，我们认为他们是没有意义的，我们并不希望输出一个字符对应的 ASCII 码或者输出一个布尔值对应的 0/1，我们该怎么做呢？答案是使用 `= delete` 说明符删除函数。

```cpp
void printInt(int x) {
    std::cout << x << '\n';
}

void printInt(char) = delete; // calls to this function will halt compilation
void printInt(bool) = delete; // calls to this function will halt compilation

int main() {
    printInt(97);   // okay

    printInt('a');  // compile error: function deleted
    printInt(true); // compile error: function deleted

    printInt(5.0);  // compile error: ambiguous match

    return 0;
}
```

当我们调用一个已经被 `= delete` 的函数时，编译器将会报告一个编译错误，告诉我们这个函数已经被删除了。

!!! tip
    `= delete` 表示的是“这个函数不应该被调用”，而不是“这个函数不存在”。因此被删除的函数也会参与重载函数的解析过程，`printInt(5.0)` 会出现模糊匹配问题。

!!! extra "删除所有不匹配的重载函数"
    对可能的各种参数逐一使用 `= delete` 有时候会导致代码很冗长，这时我们可以通过函数模板来删除所有不匹配的重载函数。

    ```cpp
    void printInt(int x) {
        std::cout << x << '\n';
    }

    template <typename T>
    void printInt(T x) = delete;
    ```


### 默认参数

在函数声明时，可以给一些参数指定默认值，这样在调用函数时，如果没有提供这些参数的值，编译器将使用默认值。

```cpp
void print(int x, int y=4) {
    std::cout << "x: " << x << '\n';
    std::cout << "y: " << y << '\n';
}
```

- 在函数调用中，任何显式提供的参数都从最左边开始使用，而未提供的参数将使用默认值。
- 如果为一个参数提供了默认值，那么它右边的所有参数都必须有默认值。

    下面这个声明是不允许的
    ```cpp
    void print(int x=10, int y); // not allowed
    ```

- 如果不止一个参数具有默认值，那么其中最左边的参数应当是最有可能由用户显式设置的参数

!!! note "默认参数不能多次声明"
    函数的默认参数不能再同一翻译单元中重复声明，这意味着对于具有前向声明和函数定义的函数，默认参数可以在前向声明中出现，也可以在函数定义中出现，但不可同时在两者中出现。

默认参数还必须在使用之前进行声明，例如下面的代码就是错误的

```cpp
#include <iostream>

void print(int x, int y); // forward declaration, no default argument

int main()
{
    print(3); // compile error: default argument for y hasn't been defined yet

    return 0;
}

void print(int x, int y=4)
{
    std::cout << "x: " << x << '\n';
    std::cout << "y: " << y << '\n';
}
```

因此我们通常会在前向声明中，而非函数定义中声明默认参数。同时如果前向声明位于头文件中，也便于它在被其他文件使用前观察到。

#### 默认参数可能导致模糊匹配

默认参数很容易导致调用的函数不明确，例如

```cpp
void foo(int x = 0) {}
void foo(double d = 0.0) {}
int main()
{
    foo(); // ambiguous function call

    return 0;
}
```

在这里，编译器无法判断调用 `foo(int)` 还是 `foo(double)`。

我们还有另一个例子：

```cpp
void print(int x);                  // signature print(int)
void print(int x, int y = 10);      // signature print(int, int)
void print(int x, double y = 20.5); // signature print(int, double)

int main()
{
    print(1, 2);   // will resolve to print(int, int)
    print(1, 2.5); // will resolve to print(int, double)
    print(1);      // ambiguous function call

    return 0;
}
```

对于 `print(1)` 而言，编译器无法判断调用`print(int)`、`print(int, int)` 还是 `print(int, double)`。

### 函数模板

函数模板是指一种通用的函数定义，它使用一个或多个类型参数来定义一个函数的模板，在使用时编译器可以使用单个模板来生成一系列相关的函数。

创建函数模板时，我们使用占位符类型（placeholder type）来代替函数的参数类型、返回类型或者函数体中将要被使用的类型。在使用函数模板前，我们需要进行**模板参数声明（template parameter declaration）**，模板参数声明的范围仅限于跟在它后面的函数模板（或类模板）。因此，每个函数模板（类模板）都需要有自己的模板参数声明。

```cpp
template <typename T> // this is the template parameter declaration
T max(T x, T y) // this is the function template definition for max<T>
{
    return (x < y) ? y : x;
}
```

在模板参数声明中，我们以关键字`template`开头，它告诉编译器我们正在创建一个模板。接下来，我们在尖括号 ( `<>` ) 内指定模板将使用的所有模板参数。对于每个类型模板参数，我们使用关键字`typename`或`class` ，后跟类型模板参数的名称（例如`T`）。

当函数模板被实例化时，编译器将模板形参替换为模板实参，然后编译生成的实例化函数。函数是否编译取决于每种类型的对象在函数内的使用方式。

从函数模板创建具有特定类型的函数的过程称为**函数模板实例化**（function template instantiation，或简称**实例化**）。当函数因函数调用而实例化时，称为**隐式实例化**。从模板实例化得到的函数在技术上称为**特化**，但在通用语言中通常称为**函数实例**。

!!! example
    ```cpp
    #include <iostream>

    template <typename T>
    T max(T x, T y)
    {
        return (x < y) ? y : x;
    }

    int main()
    {
        std::cout << max(3, 5) << '\n'; // instantiates and calls max<int>(int, int)
        std::cout << max(3.0, 5.0) << '\n'; // instantiates and calls max<double>(double, double)

        return 0;
    }
    ```

    在这个例子中，`max(3, 5)` 实例化为 `max<int>(int, int)`，而 `max(3.0, 5.0)` 实例化为 `max<double>(double, double)`。

    我们还可以指定函数模板实例化某种特定的参数，例如我们可以使用 `max<long>(1, 2)` 来实例化得到 `max<long>(long, long)`，其中 `1` 和 `2` 会被隐式转换为 `long`。

#### 具有多个模板参数的函数模板

现在我们考虑下面的程序

```cpp
template <typename T>
T max(T x, T y)
{
    return (x < y) ? y : x;
}

int main()
{
    std::cout << max(2, 3.5) << '\n';  // compile error

    return 0;
}
```

在调用 `max(2, 3.5)` 时，我们传递了 `int` 和 `double` 两种不同类型的参数，编译器首先发现并不存在一个 `max(int, double)` 的函数。接着在编译器寻找函数模板时，发现了 `max<T>(T, T)`，但是这里要求函数的两个参数是相同类型的，于是无法找到一个匹配的函数模板，这个函数调用无法解析，编译器会报告一个编译错误。

为了解决这个问题，我们可以对函数调用的参数进行显式类型转换，比如 `max(static_cast<double>(2), 3.5)`，或者我们也可以明确地指定函数模板把模板参数实例化为哪一种类型，比如 `max<double>(2, 3.5)`。

实际上解决这个问题的最好办法是让函数模板接受多个模板参数，这样我们就可以传递不同类型的参数给函数模板。

```cpp
template <typename T, typename U> 
T max(T x, U y)
{
    return (x < y) ? y : x;
}
```

但是这样还有一个问题，当我们调用这个函数时，返回值类型将始终与第一个参数的类型相同，比如 `max(2, 3.5)` 的返回值将是 `3` 而非 `3.5`。这种情况下我们可以使用 `auto` 来很方便的解决这个问题：

```cpp
template <typename T, typename U> 
auto max(T x, U y)
{
    return (x < y) ? y : x;
}
```

这样一来 `max(2, 3.5)` 的返回值将是 `3.5`，符合我们的预期。

!!! info "前向声明"
    如果我们要对一个函数进行前向声明，那么我们必须明确返回类型，这代表着我们不能直接使用 `auto` 来声明函数模板。但是我们还可以使用 `std::common_type_t` 来获取 `T` 和 `U` 的通用类型作为我们的显式返回类型。

    ```cpp
    #include <iostream>
    #include <type_traits> // for std::common_type_t

    template <typename T, typename U>
    auto max(T x, U y) -> std::common_type_t<T, U>; // returns the common type of T and U

    int main()
    {
        std::cout << max(2, 3.5) << '\n';

        return 0;
    }

    template <typename T, typename U>
    auto max(T x, U y) -> std::common_type_t<T, U>
    {
        return (x < y) ? y : x;
    }
    ```

!!! extra "缩写函数模板"
    C++20 引入了`auto`关键字的新用法：当`auto`关键字在普通函数中用作参数类型时，编译器会自动将函数转换为函数模板，每个`auto`参数成为*独立*的模板类型参数。这种创建函数模板的方法称为**缩写函数模板（abbreviated function template）**。

    例如

    ```cpp
    auto max(auto x, auto y)
    {
        return (x < y) ? y : x;
    }
    ```

    就相当于

    ```cpp
    template <typename T, typename U> 
    auto max(T x, U y)
    {
        return (x < y) ? y : x;
    }
    ```

    如果我们希望函数模板里的每一个模板类型参数都是独立的类型，就可以选择使用上面的缩写函数模板。但如果我们想让多个模板类型参数是相同的类型，那么目前并没有更简洁的方法来实现下面的写法：

    ```cpp
    template <typename T>
    T max(T x, T y) // two parameters of the same type
    {
        return (x < y) ? y : x;
    }
    ```

#### 非类型模板参数

**非类型模板参数（non-type template parameter）**是指具体有固定类型的模板参数，它以占位符的形式作为模板参数向函数传递 constexpr 值。

非类型模板参数可以是以下的任何类型：

- 整型
- 枚举类型
- `std::nullptr_t`
- 浮点型（从 C++20 开始）
- 指向一个对象的指针或引用
- 指向函数的指针或引用
- 指向成员函数的指针或引用
- 字面量类类型（从 C++20 开始）

!!! example 
    ```cpp
    #include <iostream>

    template <int N> // declare a non-type template parameter of type int named N
    void print()
    {
        std::cout << N << '\n'; // use value of N here
    }

    int main()
    {
        print<5>(); // 5 is our non-type template argument

        return 0;
    }
    ```

    编译器看到函数调用 `print<5>()` 时，会实例化一个如下所示的函数

    ```cpp
    template <>
    void print<5>()
    {
        std::cout << 5 << '\n';
    }
    ```

非类型模板参数主要在我们需要将 constexpr 值传递给函数（或类类型）时使用，这样它就可以在需要常量表达式时传递数据。

从 C++17 开始，非类型模板参数可以使用`auto`让编译器从函数调用的实参中推导出非类型模板参数

```cpp
#include <iostream>

template <auto N> // deduce non-type template parameter from template argument
void print()
{
    std::cout << N << '\n';
}

int main()
{
    print<5>();   // N deduced as int `5`
    print<'c'>(); // N deduced as char `c`

    return 0;
}
```

#### 多文件程序中使用函数模板

考虑下面的程序，它并不能正常工作

```cpp title="main.cpp"
#include <iostream>

template <typename T>
T addOne(T x); // function template forward declaration

int main()
{
    std::cout << addOne(1) << '\n';
    std::cout << addOne(2.3) << '\n';

    return 0;
}
```

```cpp title="add.cpp"
template <typename T>
T addOne(T x) // function template definition
{
    return x + 1;
}
```

在 main.cpp 中，我们调用`addOne<int>`和`addOne<double>`，但是编译器由于在这个翻译单元中看不到 `addOne()` 的定义，它无法在 main.cpp 中实例化这些函数。但是它确实看到了 `addOne` 的前向声明，并且会假设这些函数存在于其他地方，并将在稍后链接。

当编译器去编译 add.cpp 时，它会看到函数模板`addOne`的定义。但是,add.cpp 中没有使用此模板，因此编译器不会实例化任何内容。最终结果是链接器无法将对 main.cpp 中的`addOne<int>`和`addOne<double>`的调用连接到实际函数，因为这些函数从未实例化。

解决这个问题最传统的办法是把所有的模板代码都放入 `.h` 头文件中，而非 `.cpp` 源文件中

```cpp title="add.h"
#ifndef ADD_H
#define ADD_H

template <typename T>
T addOne(T x) // function template definition
{
    return x + 1;
}

#endif
```

```cpp title="main.cpp"
#include <iostream>

template <typename T>
T addOne(T x); // function template forward declaration

int main()
{
    std::cout << addOne(1) << '\n';
    std::cout << addOne(2.3) << '\n';

    return 0;
}
```

这样，任何需要访问模板的文件都可以 `#include` 相关头文件，模板定义将由预处理器复制到源文件中，然后编译器将能够实例化所需的任何函数。

### constexpr 函数

一般的函数是在程序运行时执行的，因此不能参与到一个常量表达式之中。在函数返回值类型之前加上一个 `constexpr` 关键字可以告诉编译器这个函数可以在编译时执行，因此可以用于常量表达式。

如果一个被使用的常量表达式中包含一个 constexpr 函数，那么这个 constexpr 函数调用必须在编译时求值。

要在编译时对函数进行执行，还必须满足其他两件事：

- 调用 constexpr 函数时使用的参数必须都是编译时已知的（例如常量表达式）
- constexpr 函数中的所有语句和表达式都必须可在编译时计算

!!! tip
    和 constexpr 变量类似，constexpr 函数表示的是“可以在编译时计算的函数”，这意味着它可以在常量表达式中使用，但实际上它也可能在运行时才求值，例如

    ```cpp
    #include <iostream>

    constexpr int greater(int x, int y)
    {
        return (x > y ? x : y);
    }

    int main()
    {
        int x{ 5 }; // not constexpr
        int y{ 6 }; // not constexpr

        std::cout << greater(x, y) << " is greater!\n"; // will be evaluated at runtime

        return 0;
    }
    ``` 

    在这个例子中，`x` 和 `y` 都不是常量表达式，因此在编译时 `greater(x, y)` 无法被解析，它会像一个普通函数一样在程序运行时返回结果，`constexpr` 在这种情况下没有任何作用。

当 constexpr 函数中包含了非常量表达式，或者其他的非 constexpr 函数时，它就无法在编译时被运算，这时它也无法被用在常量表达式中。

因此 constexpr 函数需要被用在常量表达式之中时，它必须在编译时被计算。在我们把 constexpr 函数用在常量表达式中时最好随时对代码进行编译、检查，以测试它是否能通过编译。

!!! info "constexpr/consteval 函数的参数并不是 constexpr 的"
    constexpr 函数的参数并不是隐式的 constexpr 变量，也不能声明为 `constexpr`，因此他们不能在函数内的常量表达式中使用。

    ```cpp
    constexpr int foo(int b)    // b is not constexpr, and cannot be used in constant expressions
    {
        constexpr int b2 { b }; // compile error: constexpr variable requires constant expression initializer

        return goo(b);          // compile error: consteval function call requires constant expression argument
    }

    int main()
    {
        constexpr int a { 5 };

        std::cout << foo(a); // okay: constant expression a can be used as argument to constexpr function foo()

        return 0;
    }
    ```

    在这个例子中，constexpr 函数的参数 `b` 不是 constexpr 的（即使函数调用中的 `a` 时一个常量表达式），它不能被用在需要常量表达式的任何地方。

    - constexpr 函数的参数可以声明为 `const` ，但在这种情况下，它们仅被视为一般的运行时常量。

#### constexpr 函数是隐式内联的

当 constexpr 函数在编译时被计算时，编译器必须在这个 constexpr 函数被调用前知道它的完整定义，这样编译器才可以在编译时就用函数的结果替换掉这个函数所在的调用语句。

在这种情况下，仅仅是前向声明是不够的，这意味着被多个文件使用的 constexpr 函数的定义需要被包含到每一个翻译单元中来——这通常会违反单一定义原则（one-definition rule，多个翻译单元中不能出现完全一致的函数定义，除非这个函数是内联函数）。为了避免此类问题，constexpr 函数是隐式内联的，这使得它们不受单一定义规则的约束。

因此，constexpr 函数通常在头文件中定义，因此可以将它们 `#included` 到任何需要完整定义的 .cpp 文件中。

!!! abstract
    - 被单个源文件 (.cpp) 中使用的 constexpr/consteval 函数应在使用它们的上方源文件中定义。
    - 被多个源文件中使用的 constexpr/consteval 函数应在头文件中定义，以便它们可以包含在每个源文件中。

!!! property "编译时求值的 constexpr 函数的前向声明"
    根据 CWG2166 ，对在编译时求值的 constexpr 函数的前向声明的要求实际上是“constexpr 函数必须在最终导致调用的最外层计算之前定义”。因此，下面的代码是有效的

    ```cpp
    #include <iostream>

    constexpr int foo(int);

    constexpr int goo(int c)
    {
        return foo(c);   // note that foo is not defined yet
    }

    constexpr int foo(int b) // okay because foo is still defined before any calls to goo
    {
        return b;
    }

    int main()
    {
        constexpr int a{ goo(5) }; // this is the outermost invocation

        return 0;
    }
    ```

    这么做的目的是允许 constexpr 函数进行相互递归。

#### consteval

当 constexpr 函数不在常量表达式中调用时，我们没办法保证编译器在编译时求值这个函数。解决这个问题的最简单粗暴的方法是用这个 constexpr 函数的返回值去初始化一个变量，但这样就会导致一个不必要的变量，也会使得代码变得冗长且丑陋。


为了解决这个问题，C++20 引入了 `consteval` 关键字，它表示这个函数必须在编译时求值，否则将导致编译错误。

```cpp
#include <iostream>

consteval int greater(int x, int y) // function is now consteval
{
    return (x > y ? x : y);
}

int main()
{
    constexpr int g { greater(5, 6) };              // ok: will evaluate at compile-time
    std::cout << g << '\n';

    std::cout << greater(5, 6) << " is greater!\n"; // ok: will evaluate at compile-time

    int x{ 5 }; // not constexpr
    std::cout << greater(x, 6) << " is greater!\n"; // error: consteval functions must evaluate at compile-time

    return 0;
}
```

上面的例子中，对 `greater(x, 6)` 的调用无法在编译时求值，因此会导致编译错误。

consteval 函数的缺点是此类函数无法在运行时求值，这使得它不如 constexpr 函数灵活——后者可以在编译时或运行时执行。因此，有一种使得 constexpr 函数在编译时强制执行的方法还是很有用的，这样我们就可以在可能时让函数在编译时求值，不能时就在运行时求值。

C++20 为 constexpr 函数提供了实现这一目标的方法——使用一个简洁的辅助函数

```cpp
#include <iostream>

// Uses abbreviated function template (C++20) and `auto` return type to make this function work with any type of value
// See 'related content' box below for more info (you don't need to know how these work to use this function)
consteval auto compileTimeEval(auto value)
{
    return value;
}

constexpr int greater(int x, int y) // function is constexpr
{
    return (x > y ? x : y);
}

int main()
{
    std::cout << greater(5, 6) << '\n';                  // may or may not execute at compile-time
    std::cout << compileTimeEval(greater(5, 6)) << '\n'; // will execute at compile-time

    int x { 5 };
    std::cout << greater(x, 6) << '\n';                  // we can still call the constexpr version at runtime if we wish

    return 0;
}
```

这里我们使用一个 constexpr 函数的返回值作为 consteval 函数的参数，这样我们就可以在编译时强制执行这个 constexpr 函数。

!!! tip
    这里我们让 consteval 函数按值返回，虽然这在运行时执行可能效率低下（如果这个值是复制成本高昂的类型，例如 `std::string`），但在编译中执行时，这不是什么问题。

在 constexpr 或 consteval 函数中，我们可以使用非 constexpr 的局部变量，并且可以更改这些变量的值。因为编译器本质上会“执行”这个函数并返回函数的结果。

```cpp
consteval int doSomething(int x, int y) // function is consteval
{
    x = x + 2;       // we can modify the value of non-const function parameters

    int z { x + y }; // we can instantiate non-const local variables
    if (x > y)
        z = z - 1;   // and then modify their values

    return z;
}
```

!!! shortcoming "不使用 constexpr 函数的原因"
    - 如果某个函数无法作为常量表达式的一部分进行计算，则不应将其标记为 `constexpr`
    - `constexpr` 是函数接口的一部分。一旦函数被设为 constexpr，它就可以被其他 constexpr 函数调用或在需要常量表达式的上下文中使用。稍后删除 `constexpr` 将破坏此类代码
    - constexpr 使函数更难调试，因为我们无法在运行时检查它们

---

## 引用

在 C++ 中，**引用（reference）**相当于现有对象的一个别名（这里的“对象”不特指 OOP 中的 object，而是泛指变量、常量、函数等）。定义了一个引用之后（明确了它所引用的对象之后），对引用的任何操作都相当于对原对象的操作。

### 左值引用

我们通常所说的引用一般指的都是左值引用（lvalue reference），它是通过 `&` 符号来定义的。

```cpp
int        // a normal int type (not an reference)
int&       // an lvalue reference to an int object
double&    // an lvalue reference to a double object
const int& // an lvalue reference to a const int object
```

- 通常所说的左值引用是对一个非 const 变量的引用，即**非 const 左值引用**（**lvalue reference to non-const** or **non-const lvalue reference**）
- 对一个 const 变量的引用称为**const 左值引用**（**lvalue reference to const** or **const lvalue reference**）

引用在初始化时必须明确它所绑定的对象，这意味着引用必须在定义时初始化，且一旦初始化后，它将一直绑定到同一个对象。

- 绑定引用的过程称为**引用绑定**（reference binding）。
- 非常量左值引用只能绑定到可修改的左值上

```cpp
int& invalidRef;   // error: references must be initialized

int x { 5 };
int& ref { x };         // okay

const int y { 5 };
// invalid: non-const lvalue reference can't bind to a non-modifiable lvalue
int& invalidRef { y }; 
// invalid: non-const lvalue reference can't bind to an rvalue 
int& invalidRef2 { 0 }; 
```

如果我们尝试把引用绑定到另一种类型的变量上，编译器会尝试先对这个变量进行隐式类型转换，然后把引用绑定到转换的结果上。但由于隐式类型转换的结果是一个右值，而非 const 左值引用不能绑定到右值上，这就会导致编译错误。

```cpp
int x { 5 };
int& ref { x };            // okay: referenced type (int) matches type of initializer

double d { 6.0 };
int& invalidRef { d };     
double& invalidRef2 { x };
```

引用也可以使用另一个引用来初始化，这样会创建一个新的引用，它绑定到原引用所绑定的对象上。

```cpp
int var{};
int& ref1{ var };  // an lvalue reference bound to var
int& ref2{ ref1 }; // an lvalue reference bound to var
```

!!! info 
    可能会有人会把`int&&`和`int**`（指向指针的指针）进行类比，认为`int&&`是对引用的引用，但实际上这表示对右值的引用（C++11）。

### 引用的生命周期

引用的生命周期和普通的变量类似：引用在定义时创建，在超出作用域时被销毁。

引用和被引用者具有相互独立的生命周期，这意味着

- 引用可以在被引用者之前被销毁
- 被引用者也可以在引用之前被销毁

当引用先被销毁时，被引用对象并不会受到任何印象，但当被引用对象先被销毁时，引用就会变成**悬空引用**（dangling reference），对悬空引用的访问会导致未定义行为。

!!! info "引用不是对象"
    在 C++ 中，引用并不是一个对象。如果可能的话，编译器会试图使用被引用对象来替代所有出现的引用，以此来优化程序，此时引用就不需要占据任何内存空间，甚至不需要实际存在；但有时候这种替代无法做到的，这时候引用就会占据实际的内存空间。

    因为引用不是一个对象，所以我们不能创建引用的引用（左值引用必须指向一个可识别的对象）。

### const 左值引用

刚刚我们提到过，一个非 const 的左值引用不能绑定到 const 变量上，因为如果允许这么做，我们将可以通过引用来修改这个变量，这违背了 const 变量的定义。如果我们希望引用一个 const 变量，我们可以使用 const 左值引用。

```cpp
const int x { 5 };
const int& ref { x };
```

我们也可以对一个非 const 变量使用一个 const 引用，此时通过引用来访问的对象将被视为常量，这表示我们不能通过这个引用来修改这个对象。

```cpp
int x { 5 };
const int& ref { x };
```

!!! note "使用右值初始化 const 左值引用"
    const 左值引用可以绑定到右值上

    ```cpp
    const int& ref { 5 }; // okay: 5 is an rvalue
    ```

    此时将会创建一个临时对象，这个临时对象被右值 5 初始化，然后 const 引用绑定到这个临时对象上。

    之前我们提到过，隐式类型转换的结果是一个右值，这意味着我们可以使用一个类型的 const 引用绑定到另一种类型的对象上去，但此时引用实际上绑定的是临时变量而非原对象，对原对象的任何修改都不会影响到引用。

    ```cpp
    short x { 1 };
    const int& ref { x };

    x--;
    std::cout << ref; // output 1
    ```

为了上述情况中避免临时变量被销毁而导致的悬空引用，C++ 有一个特殊的规则：当 const 左值引用被绑定到一个临时变量上时，临时变量的生命周期将会被延长，直到这个引用的生命周期结束，这个临时变量才会被销毁。

!!! extra "constexpr 左值引用"
    constexpr 允许被应用到引用上时，这个引用将会被允许出现在常量表达式中，但此时这个引用只能绑定到具有静态存储期的对象上（全局变量或静态局部变量）。

    当 constexpr 引用绑定到一个 const 变量上时，我们需要同时使用 constexpr（引用） 和 const（所引用的对象）

    ```cpp
    static const int s_x { 6 };        // a const int
    constexpr const int& ref2 { s_x }; // needs both constexpr and const
    ```

    介于以上的限制，constexpr 引用通常很少出现。

### 按引用传递参数

在函数调用中按值传递参数时，参数会被复制出一个副本以供函数使用，这样会导致一定的开销，尤其是当参数是一个较大的对象时。为了避免这种开销，我们可以使用引用来传递参数。

按引用传递参数时，我们可以在函数内部通过引用来对原对象进行修改，其作用和按地址传递参数类似，但是引用的语法更加简洁。

```cpp
void addOne_ref(int& x) // reference version
{
    x++;
}


void addOne_ptr(int* x) // pointer version
{
    (*x)++;
}
```

- 如果我们不希望函数修改传入的参数，我们可以使用 const 引用来传递参数，这样函数就无法修改传入的参数了。

    ```cpp
    void print(const int& x) // reference version
    {
        std::cout << x << '\n';
    }
    ```

!!! note "把不同类型的参数传递给 const 引用参数"
    之前我们提到过，const 引用可以绑定到不同类型的对象上，但此时引用实际上绑定的是临时变量而非原对象，对原对象的任何修改都不会影响到引用。

    当我们把不同类型的参数传递给 const 引用参数时，也会发生同样的事情。

    ```cpp
    #include <iostream>

    void printAddr_ref(const int& y) {
        std::cout << &y << '\n';
    }

    int main()
    {
        double x { 2.0 };
        std::cout << &x << '\n';

        printAddr_ref(x);

        return 0;
    }
    ```

    在我的一次本地运行中，这个程序的输出是

    ```
    0x7ffcc45a1080
    0x7ffcc45a107c
    ```

    函数中 `y` 的地址和 main 函数中的 `x` 的地址不同，这表明函数内引用绑定的是一个临时变量而非原对象。

!!! question "该用 `std::string_view` 还是 `const std::string&`？"
    在 C++ 中，被作为参数传递的字符串通常是 `std::string`、`std::string_view` 或 C 风格字符串（`const char*`）。     

    | Argument Type            | `std::string_view` parameter | `const std::string&` parameter               |
    |--------------------------|------------------------------|----------------------------------------------|
    | `std::string`              | Inexpensive conversion       | Inexpensive reference binding                |
    | `std::string_view`         | Inexpensive copy             | Expensive explicit conversion to std::string |
    | C-style string / literal | Inexpensive conversion       | Expensive conversion                         |

    1. 函数中的形参是 `std::string_view`，
    
        - 如果传入的是 `std::string` 或 C 风格字符串，那么这个字符串会被转换为 `std::string_view`，但这个转换并不会带来太大的开销
        - 如果传入的是 `std::string_view`，那么这个字符串会被直接按值传递给函数，发生一次开销不大的复制

    2. 函数中的形参是 `const std::string&`，

        - 如果传入的是 `std::string`，引用会直接绑定到传递过来的 `std::string` 上
        - 如果传入的是 `std::string_view` 或 C 风格字符串，那么这个字符串会先被隐式转换为 `std::string`，然后引用才会绑定到这个 `std::string` 上

    从上面的分析可以看出，`std::string_view` 作为参数通常要比 `const std::string&` 更好，因为它可以接受更多类型的参数，而且转换开销更小。

    但这并不是绝对的，在某些情况下，`const std::string&` 可能更适合，例如

    - 使用的 C++ 版本低于 C++14，此时就无法使用 `std::string_view`
    - 如果函数需要使用到字符串一些其他的属性或功能，例如需要字符串以 null 结尾（`std::string_view` 不保证它以 null 结尾）、需要调用 `std::string` 的成员函数等


---


## 命名空间

我们可以通过 `namespace` 关键字来创建命名空间，命名空间可以包含变量、函数、类等。通常来说，**命名空间的名称以大写字母为开头**

```cpp
namespace NamespaceIdentifier
{
    // content of namespace here
}
```

- 只有声明和定义可以出现在命名空间中，而不能包含赋值语句、函数调用等
- 命名空间里的函数定义可以包含函数所执行的语句
- 命名空间可以嵌套

### 作用域解析运算符

`::` 被称为作用域解析运算符（scope resolution operator），它可以用于访问命名空间中的变量、函数等。

- `::` 的左侧是命名空间的名称，右侧是命名空间中的变量或函数
- 假如 `::` 左侧为空，则假定解析的是全局命名空间

!!! warning "不要使用 `using namespace std` 😡👊"
    使用 `using namespace std` 可能会导致命名空间 `std` 中定义的变量和函数与我们自己定义的变量和函数发生冲突，因此不推荐使用。

### 命名空间的标识符解析

如果我们在一个命名空间中使用标识符（变量或函数的名称）时没有提供解析范围，编译器会先尝试在本地命名空间中寻找匹配的标识符，如果没有找到则逐一向上查找直到找到匹配的标识符，或到达全局命名空间。

我们知道，在 `::` 左侧为空时，假定解析的是全局命名空间，虽然这在多数情况下是多余的，因为全局命名空间是默认的，但在某些情况下，我们可能需要使用 `::` 来明确指出我们要访问的是全局命名空间中的变量或函数。

```cpp
#include <iostream>

void print() // this print() lives in the global namespace
{
	std::cout << " there\n";
}

namespace Foo
{
	void print() // this print() lives in the Foo namespace
	{
		std::cout << "Hello";
	}

	void printHelloThere()
	{
		print();   // calls print() in Foo namespace
		::print(); // calls print() in global namespace
	}
}

int main()
{
	Foo::printHelloThere();

	return 0;
}
```

### 命名空间中的前向声明

当我们使用一个命名空间中的函数时，也需要进行前向声明，通常而言我们可以直接 `#include` 命名空间所在的 `.h` 文件


```cpp title="add.h"
#ifndef ADD_H
#define ADD_H

namespace BasicMath
{
    // function add() is part of namespace BasicMath
    int add(int x, int y);
}

#endif
```

```cpp title="add.cpp"
#include "add.h"

namespace BasicMath
{
    // define the function add() inside namespace BasicMath
    int add(int x, int y)
    {
        return x + y;
    }
}
```

```cpp title="main.cpp"
#include "add.h" // for BasicMath::add()

#include <iostream>

int main()
{
    std::cout << BasicMath::add(4, 3) << '\n';

    return 0;
}
```

编译时使用 `g++/clang main.cpp add.cpp -o main`

### 嵌套命名空间

命名空间可以在另一个命名空间中定义，例如

```cpp title="C++17 之前"
namespace Foo
{
    namespace Goo // Goo is a namespace inside the Foo namespace
    {
        int add(int x, int y)
        {
            return x + y;
        }
    }
}
```

从 C++17 开始，我们也可以这样定义：

``` cpp title="C++17 及之后"
namespace Foo::Goo // Goo is a namespace inside the Foo namespace (C++17 style)
{
    int add(int x, int y)
    {
        return x + y;
    }
}
```

### 命名空间别名（namespace alias）

由于使用嵌套命名空间中的变量或者函数会很冗长，因此我们可以选择创建一个命名空间别名来简化代码。

```cpp
#include <iostream>

namespace Foo::Goo
{
    int add(int x, int y)
    {
        return x + y;
    }
}

int main()
{
    namespace Active = Foo::Goo; // active now refers to Foo::Goo

    std::cout << Active::add(1, 2) << '\n'; // This is really Foo::Goo::add()

    return 0;
} // The Active alias ends here
```

!!! info "命名空间的使用"
    向大众分发的代码应该使用命名空间，以避免其与其他代码发生冲突，通常一个顶级命名空间就足够了（例如 `MyProject`）。
    
    这么做还有一个优点：在绝大多数代码编辑器中键入命名空间的名称之后，再输入 `::` 编辑器就会自动显示该命名空间中的所有函数和变量作为补全的候选项。

### 未命名（匿名）命名空间

**未命名命名空间**（也称为**匿名命名空间**）是没有名称定义的命名空间，如下所示：

```cpp
#include <iostream>

namespace // unnamed namespace
{
    void doSomething() // can only be accessed in this file
    {
        std::cout << "v1\n";
    }
}

int main()
{
    doSomething(); // we can call doSomething() without a namespace prefix

    return 0;
}
```

在未命名命名空间中声明的所有内容都被视为父命名空间的一部分，因此函数或变量即使是在未命名命名空间中声明的，也可以在父命名空间（上例时全局命名空间）中访问。

这看起来似乎没什么用处，但未命名命名空间中的所有标识符都不可以被其他文件访问（或者说具有*内部链接*），效果相当于 `static` 关键字。

当我们有大量内容仅允许当前的翻译单元使用时，使用匿名命名空间显然要比给所有的标识符都加上 `static` 更加方便。


---


## 其他

### 头文件

使用头文件的目的是为了将函数声明和定义分离，使得代码更加清晰易读。

为了避免头文件被多次包含，我们可以使用 `#pragma once` 或者 `#ifndef`、`#define`、`#endif` 来防止头文件被多次包含。

```cpp title="add.h（使用 #ifndef）"
#ifndef ADD_H
#define ADD_H

int add(int x, int y);

#endif
```

```cpp title="add.cpp"
#include "add.h"

int add(int x, int y) {
    return x + y;
}
```

```cpp title="main.cpp"
#include "add.h"

int main() {
    int x { 1 };
    int y { 2 };
    int z = add(x, y);
    return 0;
}
```

当然，我们也可以使用 `#pragma once` 来防止头文件被多次包含。

```cpp title="add.h（使用 #pragma once）"
#pragma once

int add(int x, int y);
```

!!! note 
    由于 `#pragma once` 不是由 C++ 标准定义的，因此某些编译器可能不会实现它，使用 `#ifndef`、`#define`、`#endif` 是更加通用的做法。但对于 modern C++ 编译器来说，`#pragma once` 是一个更加简洁的选择。

### 输入输出

!!! tip "`std::endl` vs `'\n'`"
    - `std::endl` 实际上会完成两件事：输出一个换行符并刷新输出缓冲区
    - `'\n'` 只会输出一个换行符。

    由于刷新缓冲区是一个相对耗时的操作，因此在大多数情况下，我们应该使用 `'\n'` 而不是 `std::endl`。

!!! note "副作用"
    `std::cout << "Hello " << "world!"` 的副作用是输出 `"Hello world!"`，它实际上相当于 `(std::cout << "Hello ") << "world!"`，括号内的返回值是 `std::cout`，因此可以连续使用 `<<` 运算符，先输出 `"Hello "`，再输出 `"world!"`。

!!! note "布尔变量"
    === "输出"
        `std::cout` 会将 `true` 输出为 `1`，将 `false` 输出为 `0`。
        例如
        ```cpp
        std::cout << true << '\n';  // true evaluates to 1
        std::cout << !true << '\n'; // !true evaluates to 0

        bool b {false};
        std::cout << b << '\n';     // b is false, which evaluates to 0
        std::cout << !b << '\n';    // !b is true, which evaluates to 1
        ```
        输出结果为
        ```
        1
        0
        0
        1
        ```

        如果我们想要输出 `true` 或 `false`，我们可以使用 `std::boolalpha` 操纵符。
        ```cpp
        std::cout << true << '\n';
        std::cout << false << '\n';

        std::cout << std::boolalpha; // print bools as true or false

        std::cout << true << '\n';
        std::cout << false << '\n';
        ```
        输出结果为
        ```
        1
        0
        true
        false
        ```
        我们也可以用 `std::noboolalpha` 操纵符来恢复默认的输出格式。

    === "输入"
        通常而言，我们只能用 `std::cin` 给 `b` 输入 `1` 或 `0` 来读取布尔值，其他的输入（包括其他数字和字符）会导致输入的失败，使得变量 `b` 被设置为 `false`，并且报错。
        
        但我们也可以使用 `std::boolalpha` 操纵符来读取 `true` 或 `false`。
        ```cpp
        bool b{};
        std::cin >> std::boolalpha;
	    std::cin >> b;
        ```

        !!! tip
            对 `std::cin` 使用 `std::boolalpha` 后会使得它只接受小写的 `false` 和 `true`，而不接受任何其他输入（包括大写字母的变体和任何数字）。

!!! note "提取空白字符"
    直接使用 `std::cin` 提取字符会忽略前导和数据之间的空白字符，

    ```cpp
    char ch{};
    std::cin >> ch; // extracts a, leaves " b\n" in stream
    std::cout << "You entered: " << ch << '\n';

    std::cin >> ch; // skips leading whitespace (the space), extracts b, leaves "\n" in stream
    std::cout << "You entered: " << ch << '\n';
    ```

    输入流为 `a b`，输出结果为

    ```
    You entered: a
    You entered: b
    ```

    如果我们想要把空白字符也提取到 `ch` 中，我们可以使用 `std::cin.get(ch)`。

    ```cpp
    char ch{};
    std::cin.get(ch); // extracts a, leaves " b\n" in stream
    std::cout << "You entered: " << ch << '\n';

    std::cin.get(ch); // extracts space, leaves "b\n" in stream
    std::cout << "You entered: " << ch << '\n';
    ```

    输入流不变，输出结果为

    ```
    You entered: a
    You entered:  
    ```

### 逻辑异或

在 C++ 中并没有提供逻辑异或的运算符（`^` 是按位异或），但我们可以使用 `!=` 来实现逻辑异或，比如 `a` 逻辑异或 `b` 可以写成 `a != b`。

- `a != b` 等价于 `a xor b`
- `a != b != c` 等价于 `a xor b xor c`

但是这里要求 `a`、`b` 和 `c` 都是布尔值，如果我们要进行非布尔值的异或运算，我们可以用 `static_cast<bool>` 来将它们转换为布尔值，但是其实还有更简单的方法：~~（这么写的原因自己体会吧懒得解释了）~~

```cpp
!!a != !!b != !!c
```

### if constexpr statement

在 C++17 中添加了 `if constexpr` 语句，它和普通的 if 语句的不同之处在于它要求条件表达式是一个常量表达式（在编译时就能确定布尔值），因此可以实现编译时对条件分支语句进行优化：

- 当常量表达式的值是 true 时，编译器会把整个 if-else 块替换为 true 分支的代码
- 当常量表达式的值是 false 时

    - 如果有 false 分支（else case），编译器会把整个 if-else 块替换为 false 分支的代码
    - 如果没有 false 分支，编译器会把整个 if-else 块替换为空语句

```cpp
constexpr double gravity{ 9.8 };

if (gravity == 9.8) // constant expression, always true
    std::cout << "Gravity is normal.\n";   // will always be executed
else
    std::cout << "We are not on Earth.\n"; // will never be executed
```

上面的例子会被优化为

```cpp
constexpr double gravity{ 9.8 };

std::cout << "Gravity is normal.\n";
```

### assert

- 通过 `#include <cassert>` 来使用 `assert` 宏
- 当 assert 语句的计算结果为 false 时，程序将会停止执行
- 可以通过一个小技巧来使得 assert 语句更具描述性：添加一个由 `&&` 连接的字符串字面量

    ```cpp
    assert(found && "Car could not be found in database");
    ```

    当 assert failed 时，这个字符串也会出现在输出中，帮助我们更好地理解程序发生错误的原因

除了常规的 assert 之外，C++ 还提供了另一种类型的 assert 方式：`static_assert`，它是在编译时检查的，而不是在运行时检查的。格式为

```cpp
static_assert(condition, diagnostic_message)
```

例如

```cpp
static_assert(sizeof(long) == 8, "long must be 8 bytes");
static_assert(sizeof(int) >= 4, "int must be at least 4 bytes");
```

- 由于 `static_assert` 是在编译时计算的，因此 condition 必须是常量表达式
- `static_assert` 可以放置在代码文件中的任何位置（甚至在全局命名空间中）
- 在 C++17 之前，diagnostic_message 必须作为第二个参数提供；从 C++17 开始，如果省略了 diagnostic_message，编译器会自动生成一个默认的错误消息

---

## 代码风格相关

### 注释

- 对于库、文件和函数，通常把注释放在库/文件的顶部，或是紧接在函数上方
- 注释用于描述库、程序、函数的功能 

    explain what they do

- 有时注释还可用于描述代码如何实现

    explain how they do it

- 在具体的语句层面，注释通常用于解释代码的目的和原因，而非代码正在做什么

    假如代码很复杂以至于需要解释，那么通常需要重构代码而非添加注释

    explain why they do it





