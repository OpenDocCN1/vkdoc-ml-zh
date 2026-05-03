# 1. Rust 基础

在本章中，我们将探索 Rust 作为一种语言，以及 Rust 支持的独特结构和编程模型。我们还将讨论 Rust 最大的卖点，以及是什么让这门语言对机器学习应用程序特别有吸引力。

在概述了 Rust 语言之后，我们将从安装开始。然后我们将转向 Cargo，它是 Rust 的官方包管理器，以及我们如何用 Rust 创建应用程序。之后，我们将研究 Rust 的编程结构，例如变量、循环结构和所有权模型。本章最后，我们将为 Rust 代码编写单元测试，并展示如何使用标准包管理器运行这些测试。

在本章结束时，你应该对如何用 Rust 编写和编译简单应用程序有了相当的理解。

## 1.1 为什么选择 Rust？

人们普遍认为，底层系统编程语言和高级应用程序编程语言之间存在差异。公认的观点是，如果你想创建高性能的应用程序并创建在裸机上运行的库，你需要使用 C 或 C++ 这样的语言；如果你想为业务用例创建应用程序，则需要使用 Java/Python/JavaScript 等语言进行编程。

Rust 语言的目标是位于高级语言和低级语言之间的交叉点。接近底层的程序必然直接处理内存，这意味着其中没有垃圾回收机制。在高级语言中，内存是由系统为程序员管理的。

实现垃圾回收是有成本的。同时，我们现有的垃圾回收策略并不完美，在具有自动内存管理的程序中仍然存在内存泄漏的例子。高级语言中内存泄漏的主要原因之一是，当创建包以在高级语言中提供接口，但核心实现却使用低级语言时。例如，Python 库 pandas 就有不少内存泄漏。此外，没有证据并不代表证据不存在，因此没有正式证明表明引入垃圾回收策略将消除所有可能的内存泄漏。

另一个问题与引用有关。引用在原理上很容易理解，它们开销低且对于在软件创建中实现开发者效率至关重要。因此，像 C 和 C++ 这样针对底层软件创建的语言允许无限制地创建引用和修改被引用的对象。



## 1.2 更好的引用

通常，在对象系统中，对象存在于一个称为堆或对象存储的全局对象空间中。对象可以访问对象存储的哪一部分并没有严格的约束，因为对象引用的传递方式没有限制。这在防止聚合对象的表示暴露时会产生影响。构成聚合对象的组件被认为包含在该聚合对象内部，并且是其表示的一部分。但是，由于对象存储是全局的，通常无法阻止其他对象访问该表示。使用标准的引用语义来强制执行包含的概念是不可能的。

一个更好的解决方案可以是限制所创建的不同类型对象的可见性。这是通过声明所有对象都有一个与之关联的上下文来实现的。从系统根节点出发的所有路径都必须经过对象的所有者。

在 Rust 中，类型维护了一些关键的不变性，这里列出它们。首先，每个存储位置都保证具有以下两种情况之一：

*   1 个可变引用和 0 个不可变引用，或者
*   0 个可变引用和 n 个不可变引用。

我们将在本章的后面部分看到这如何转化为实际代码。这个不变性通过禁止对单个内存位置进行并发读写，从而防止了并发系统中的竞态条件。然而，仅凭这个不变性本身并不足以保证内存安全，尤其是在存在可移动对象的情况下。例如，由于一个给定的变量在对象被移动后变得不可用，与该变量关联的存储位置可能会被重用或释放。结果，任何先前创建的引用在移动后都将成为悬垂引用。

这个问题也通过 Rust 之前的所有权规则得到了解决。对对象的引用是通过其所有者传递创建的。类型系统保证在引用未完成时，所有者不会在移动后发生变化。相反，类型系统允许在没有未完成引用时更改所有权。本章后面部分将更详细地讨论这方面的例子。

所有刚才提到的内容在机器学习应用中甚至更为重要。训练机器学习应用涉及大量数据，并且数据的变化越多越好，这意味着在训练阶段会创建大量对象。你可能不希望出现内存错误。在模型部署后，创建的模型是矩阵和张量，它们被实现为浮点数集合。即使在不再有用之后，让过去的对象和预测结果悬垂在内存中可能也是不可取的。创建并发系统也有好处。Rust 类型保证不会出现竞态条件，因此程序员可以安全地创建试图尽可能分散计算的机器学习应用。

在所有这些关于内存安全和高性能的讨论之外，我们还有诸如类型推断这样的高级特性，因此我们不需要为所有变量编写类型。我们将在本章后面部分看到何时定义类型是重要的。从之前的讨论中得出的另一个有趣点是，在编写 Rust 代码时，我们并不需要考虑内存。因此，从使用的角度来看，感觉内存正在为我们管理。然后还有其他的结构，如闭包、迭代器和标准库，它们使得用 Rust 编写代码更像是在用高级语言编写。对于机器学习应用来说，这一点至关重要。像 Python 这样的高级语言之所以在机器学习领域取得成功，是因为该语言的表达能力支持自由形式的实验和开发人员生产力。

在本章中，我们将了解 Rust 的基础知识以及使 Rust 成为其自身语言的编程结构。我们主要涵盖诸如结构体和枚举之类的主题，这些主题在这种语言中看起来和感觉上有所不同，并且可能不是我们在这种语言中所期望的。我们将跳过许多重要的事情，例如单独的数据类型，它们与 C 和 Java 等其他语言类似。Rust 的核心设计之一是，编程感觉应该与更流行的 C 和 Java 相同，这样来自这些语言的程序员就不必进行大量的思维改造，同时还能获得许多以前未曾考虑过的内存优势。

## 1.3 Rust 安装

在本节中，我们将探讨如何根据操作系统安装 Rust。显示了命令和可能的输出。

```
$ curl https://sh.rustup.rs -sSf | sh
info: downloading installer
Welcome to Rust!
This will download and install the official compiler for the Rust programming language, and its package manager, Cargo.
It will add the cargo, rustc, rustup and other commands to Cargo's bin directory, located at:
/home/ubuntu/.cargo/bin
This path will then be added to your PATH environment variable by modifying the profile file located at:
/home/ubuntu/.profile
You can uninstall at any time with rustup self uninstall and these changes will be reverted.
Current installation options:
default host triple: x86_64-unknown-linux-gnu
default toolchain: stable
modify PATH variable: yes
1) Proceed with installation (default)
2) Customize installation
3) Cancel installation
>1
info: syncing channel updates for 'stable-x86_64-unknown-linux-gnu'
info: latest update on 2019-02-28, rust version 1.33.0 (2aa4c46cf 2019-02-28)
info: downloading component 'rustc'
84.7 MiB / 84.7 MiB (100 %) 67.4 MiB/s ETA: 0 s
info: downloading component 'rust-std'
56.8 MiB / 56.8 MiB (100 %) 51.6 MiB/s ETA: 0 s
info: downloading component 'cargo'
info: downloading component 'rust-docs'
info: installing component 'rustc'
84.7 MiB / 84.7 MiB (100 %) 10.8 MiB/s ETA: 0 s
info: installing component 'rust-std'
56.8 MiB / 56.8 MiB (100 %) 12.6 MiB/s ETA: 0 s
info: installing component 'cargo'
info: installing component 'rust-docs'
8.5 MiB / 8.5 MiB (100 %) 2.6 MiB/s ETA: 0 s
info: default toolchain set to 'stable'
stable installed - rustc 1.33.0 (2aa4c46cf 2019-02-28)
Rust is installed now. Great!
To get started, you need Cargo's bin directory ($HOME/.cargo/bin) in your PATHenvironment variable. Next time you log in this will be done automatically .
To configure your current shell, run source $HOME/.cargo/env
```

如果我们研究前面的输出，将会看到以下几点。

*   `rustup` 脚本已成功识别我的发行版，并将安装与之兼容的 Rust 二进制文件。
*   通过此命令将运行 Rust 以及 Cargo（官方 Rust 包管理器）的安装。
*   这些命令将被添加到 `<home/>.cargo/bin` 目录，并可从命令行访问。

## 1.4 包管理器与 Cargo

Cargo 是一个用于开发 Rust 应用程序和库的便捷构建工具。包信息应保存在一个 toml（Tom's Obvious, Minimal Language）文件中。toml 文件格式相对较新，根据 github 上的 toml 仓库，它被设计为能够明确地映射到一个哈希表。



## 1.5 在 Rust 中创建新应用

在 Rust 中创建新应用非常简单。

```
$ cargo new myfirstapp
Created binary (application) `myfirstapp` package
```

检查 `Cargo.toml` 文件。你应该会看到类似下面的内容。

```
[package]
name = "myfirstapp"
version = "0.1.0"
authors = ["Joydeep Bhattacharjee"]
edition = "2018"
[dependencies]
```

如你所见，这里添加了一些基本信息。重要的项是名称和版本。

如果你检查 `src/` 文件夹的内容，你还会看到一个 `main.rs` 文件。检查 `main.rs` 文件的内容。你会看到一个包含 `main` 函数的最小文件。要运行一个 Rust 应用，你需要一个 `main` 函数作为代码的入口点。这里的代码是一个简单的打印 "hello world"。

```
fn main() {
println!("Hello, world!");
}
```

现在我们可以使用 `build` 命令来构建应用。这将生成一个可用于运行应用的二进制文件。一旦应用开发完成，我们可以使用 `--release` 标志来创建一个优化后的二进制文件。这样做是必要的，因为默认情况下，cargo 构建会禁用许多优化，以便于测试。因此，在创建用于生产环境的构建时，应该使用 release 标志。

```
$ cargo build
Compiling myfirstapp v0.1.0 (/tmp/myfirstapp)
Finished dev [unoptimized + debuginfo] target(s) in 8.47s
$ ls target/debug/myfirstapp
target/debug/myfirstapp
$ ./target/debug/myfirstapp
Hello, world!
```

在开发过程中，我们也可以使用 `cargo run` 命令来简化上述流程。

```
$ cargo run
Finished dev [unoptimized + debuginfo] target(s) in 0.42s
Running `target/debug/myfirstapp`
Hello, world!
```

## 1.6 Rust 中的变量

在 Rust 中，变量使用 `let` 关键字定义。变量的类型会被自动推断。请看下一个例子。

```
let x = "learning rust";
println!("{}", x);
```

`println` 用于查看变量。

这里需要注意 `println!` 这个特殊结构。当你在函数名后面看到 `!` 符号时，这意味着正在使用一个宏。宏是 Rust 中特殊的元编程结构，这超出了本书的范围。之所以使用 `println` 宏，是因为 Rust 不支持函数中的可变参数，因此 `println` 必须是一个宏。

我们可以使用以下代码查看变量的类型。

```
#![feature(core_intrinsics)]
fn print_type_of(_: &T) {
println!("{}", unsafe { std::intrinsics::type_name::() });
}
fn main() {
let x = "learning rust";
println!("{}", x);
print_type_of(&x);
}
```

不过，这段代码不能在默认的稳定版本中运行，需要在 nightly 版本中编译。需要启用 nightly 编译器。Nightly 版本是存放不稳定或潜在不稳定代码的地方，因此我们正在讨论的这种语言特性只在 nightly 版本中可用。

```
$ rustup default nightly
```

现在我们应该能够运行这段代码了。

```
$ ./variables1
learning rust
&str
```

现在尝试使用不同类型的变量。

```
let x = "learning rust";
let y = 6;
let z = 3.14;
println!("{}", x);
println!("type of x:");
print_type_of(&x);
println!("type of y:");
print_type_of(&y);
println!("type of z:");
print_type_of(&z);
```

使用上述代码的输出是：

```
$ ./variables1
learning rust
type of x:
&str
type of y:
i32
type of z:
f64
```

### 注意

`i32` 本质上是 32 位整数，`f64` 是 64 位浮点数。我们将在本书中讨论不同类型的数字，但 Rust 主要遵循不同语言中通用的基本数据类型格式，以便于编译到不同的架构。

### 1.6.1 可变性与遮蔽

使用 `let` 关键字创建的变量是不可变的。根据 Rust 官方文档，这样做是因为 Rust 的主要关注点之一是安全性。^(¹) 当需要更改变量的值时，我们可以创建可变的变量。这通过将 `mut` 关键字与 `let` 一起使用来实现。

```
let mut x = 32;
println!("Current value of x: {}", x);
x = 64;
println!("Current value of x: {}", x);
```

输出是：

```
Current value of x: 32
Current value of x: 64
```

但是，不允许更改变量的类型。

```
let mut x = 32;
println!("Current value of x: {}", x);
x = "rust";
println!("Current value of x: {}", x);
```

因此，对于像上面这样的例子，我们会得到一个错误，如下所示。

```
$ rustc variables3.rs
error[E0308]: mismatched types
--> variables3.rs:4:9
|
4 |       x = "rust";
|       ^^^^^^ expected integer, found reference
|
= note: expected type `{integer}`
found type `&'static str`
error: aborting due to previous error
For more information about this error, try `rustc --explain E0308`.
```

注意，在将 `x` 赋值为字符串的地方，编译器告诉我们代码应该是一个整数。现在传入字符串是行不通的。

对于简单的计算，我们也可以使用遮蔽原则。当一个在外部作用域中声明的变量与在内部作用域中使用的变量同名时，就会发生遮蔽。因此，像下面这样的代码是完全有效的。

```
fn main() {
let x = 1;
let x = x + 2;
let x = x * 2;
println!("Value of x: {}", x);
}
```

上述代码的输出是 `Value of x: 6`。

### 1.6.2 变量作用域

此外，在这种情况下，需要严格维护变量的作用域。让我们看一个例子。

```
// variables5.rs
fn main() {
let x = 5;
if 4 < 10 {
let x = 10;
println!("Inside if x = {:?}", x);
}
println!("Outside if x = {:?}", x);
}
```

检查输出。由于内部变量 `x` 的作用域在第一个打印语句之后结束，因此第一个打印语句打印出 `x` 为 10，而外部的打印语句打印出 5。

```
$ ./variables5
Inside if x = 10
Outside if x = 5
```

如你所见，变量的作用域得到了维护，一旦 `if` 语句的作用域结束，变量 `x` 就恢复到之前的状态。

## 1.7 数据类型

这些数据类型与你会在其他语言中找到的大致相似。请查看以下列表。

*   bool : 布尔类型。
*   char : 字符类型。
*   i8 : 8 位有符号整数类型。
*   i16 : 16 位有符号整数类型。
*   i32 : 32 位有符号整数类型。
*   i64 : 64 位有符号整数类型。
*   isize : 指针大小的有符号整数类型。
*   u8 : 8 位无符号整数类型。
*   u16 : 16 位无符号整数类型。
*   u32 : 32 位无符号整数类型。
*   u64 : 64 位无符号整数类型。
*   usize : 指针大小的无符号整数类型。
*   f32 : 32 位浮点类型。
*   f64 : 64 位浮点类型。
*   array : 固定大小的数组，表示为 `[T; N]`，其中 T 是元素类型，N 是非负的编译时常量大小。
*   slice : 一个连续序列的动态大小视图，表示为 `[T]`。
*   str : 字符串切片。
*   tuple : 有限异构序列 `(T, U, ...)`。

## 1.8 函数

函数使用 `fn` 关键字定义。定义函数时，需要说明函数的签名和参数。对于 void 函数，我们可以省略函数签名。回想一下，我们在 `main` 函数的情况下没有提供任何签名。要从函数返回，没有显式的 `return` 语句。相反，返回语句的末尾不能有分号。

```
fn main() {
println!("{:?}", square_of(-5));
}
fn square_of(x: i32) -> i32 {
println!("x = {:?}", x);
x.pow(2)
}
// output:
//     x = -5
//     25
```

## 1.9 条件判断

有两种方法可以进行条件检查。一种是普通的 `if` 块，另一种是模式匹配。



### 1.9.1 条件判断（If Conditions）

条件判断的工作方式与其他语言类似。唯一可能让你感到意外的是，在 Rust 中，`if` 代码块可以返回一个语句。这一点非常方便，因为正如你之前所见，Rust 有着严格的作用域规则。

```
// 用法：
// $ ./conditions
// "cold"
fn main() {
let place = "himalayas";
let weather = if place == "himalayas" {
"cold"
} else {
"hot"
};
println!("{:?}", weather);
}
```

### 注意

使用 `{:?}` 打印变量的 `println!` 语句，只有在变量类型实现了 `Debug` trait 时才能正常工作。我们将在后续章节中为不同的类实现 `Debug`，这将是本书中打印变量的标准方法。

### 1.9.2 模式匹配（Pattern Matching）

对于 1.9.1 节中展示的相同逻辑，我们可以使用 `match` 运算符采用更具声明性的语法。需要注意的是，所有可能的情况都必须被覆盖。上面的 `if` 语句可以写成下面这样。

```
// 用法：
// $ ./match
// "cold"
fn main() {
let place = "himalayas";
let weather = match place {
"himalayas" => "cold",
_ => "hot",
};
println!("{:?}", weather);
}
```

## 1.11 面向对象编程（Object-Oriented Programming）

Rust 既包含了丰富的函数式编程范式，也包含了面向对象编程（OOP）范式。但由于本书的读者可能来自机器学习领域，并且有兴趣学习机器学习（这是我们做出的假设），而且基于对象的编程模型在这些领域很流行（就像在几乎所有编程领域一样），我们将特别关注基于对象的编程模型。为了深入探讨这一点，我们将从核心类型开始，然后介绍 Rust 中各个部分是如何结合在一起的。

### 1.11.1 结构体（Structures）

首先是结构体。结构体本质上是具名元组，可用于存储有组织的信息。它们也可以被视为一个实例的属性。请看下面的代码。

```
// structures.rs
// $ ./structures
// Planet { co2: 0.04, nitrogen: 78.09 }
// Planet { co2: 95.32, nitrogen: 2.7 }
#[derive(Debug)]
struct Planet {
co2: f32,
nitrogen: f32
}
fn main() {
let earth = Planet { co2: 0.04, nitrogen: 78.09 };
println!("{:?}", earth);
let mars = Planet { co2: 95.32, nitrogen: 2.7 };
println!("{:?}", mars);
}
```

另外请注意，在创建结构体时，我们使用了 `Debug` trait 进行注解。这样做是因为它允许我们在 `main` 方法中以调试模式打印类实例 `earth` 和 `mars`。因此，我们将得到类似下面的输出，清晰地显示了类、变量名和值。

```
// $ ./oops
// Planet { co2: 0.04, nitrogen: 78.09 }
// Planet { co2: 95.32, nitrogen: 2.7 }
```

### 1.11.2 特质（Traits）

结构体和其他数据结构本身并不拥有功能。这些功能是通过特质来定义的。特质类似于面向对象编程语言中的接口。Rust 的核心原则之一是零成本抽象原则。用 Bjarne Stroustrup 的话来说：

> *C++ 的实现遵循零开销原则：你不使用的东西，就不需要为之付费 [Stroustrup, 1994]。更进一步说：你使用的东西，你无法手工编写出更好的代码。*
> 
> —Stroustrup

特质旨在支持零成本抽象。一个特质可以为多种类型实现，并且可以使用特质为旧类型添加新功能。下面展示了一个简单特质的例子。`co2` 和 `nitrogen` 是特质的成员，而 `summarize` 是一个方法。

```
trait Atmosphere {
fn new(co2: f32, nitrogen: f32) -> Self;
fn amount_of_other_gases(&self) -> f32;
fn summarize(&self);
}
```

### 1.11.3 方法与 impl 块（Methods and impl）

你可以使用 `impl` 代码块在结构体上实现方法。通过这种方式，我们得到了类似于对象的东西，这意味着我们拥有一个封装了数据和行为的类型。下面展示了一个结合了之前结构体和特质的例子，以及如何为特定结构体进行实现。

```
// oops.rs
#[derive(Debug)]
struct Planet {
co2: f32,
nitrogen: f32
}
trait Atmosphere {
fn new(co2: f32, nitrogen: f32) -> Self;
fn amount_of_other_gases(&self) -> f32;
fn summarize(&self);
}
impl Atmosphere for Planet {
fn new(co2: f32, nitrogen: f32) -> Planet {
Planet { co2: co2, nitrogen: nitrogen }
}
fn amount_of_other_gases(&self) -> f32 {
100.0 - self.co2 - self.nitrogen
}
fn summarize(&self) {
let other_gases = self.amount_of_other_gases();
println!("For planet {planet:?}: co2 = {co2}, nitrogen={nitrogen}, other_gases={other_gases}",
planet=self, co2=self.co2, nitrogen=self.nitrogen, other_gases=other_gases);
}
}
fn main() {
let earth = Planet { co2: 0.04, nitrogen: 78.09 };
println!("{:?}", earth);
let mars = Planet { co2: 95.32, nitrogen: 2.7 };
println!("{:?}", mars);
earth.summarize();
mars.summarize();
}
```

对于上面的代码，我们将看到下面的输出。你可以看到，在创建实例 `earth` 和 `mars` 之后，实例的 `summarize` 方法运行了，该方法计算了不同的参数并打印了结果。

```
// $ ./oops
// Planet { co2: 0.04, nitrogen: 78.09 }
// Planet { co2: 95.32, nitrogen: 2.7 }
// For planet Planet { co2: 0.04, nitrogen: 78.09 }: co2 = 0.04, nitrogen=78.09, other_gases=21.870003
// For planet Planet { co2: 95.32, nitrogen: 2.7 }: co2 = 95.32, nitrogen=2.7, other_gases=1.9800003
```

### 1.11.4 枚举（Enumerations）

枚举是一种有趣的方式，用于创建一种可以持有几个不同变体中某一个值的类型。例如，IP 地址可以是多种 IP 地址类型中的一种，但不能同时是多种。一本书可以是精装本或平装本，但不能两者都是，等等。在这种情况下，我们可以将类型定义为枚举。请看下面的代码。

```
// enumerations.rs
#[derive(Debug)]
enum NationalHolidays {
GandhiJayanti,
RepublicDay,
IndependenceDay,
}
fn inspect(day: NationalHolidays) -> String {
match day {
NationalHolidays::GandhiJayanti => String::from("Oct 2"),
NationalHolidays::RepublicDay => String::from("Jan 26"),
NationalHolidays::IndependenceDay => String::from("Aug 15"),
}
}
fn main() {
let day = NationalHolidays::GandhiJayanti;
let date = inspect(day);
println!("{:?}", date); // 输出: Oct 2
}
```

与结构体类似，我们也可以为枚举附加功能。运行此代码作为练习留给读者。

## 1.12 编写测试（Writing Tests）

最后，作为我们正在学习的 Rust 核心基础知识的一部分，我们来到了如何在 Rust 中编写测试的部分。作为示例，我们采用 `ownership1.rs` 代码，并为 `add_version` 函数编写一个测试。该方法的代码很简单，就是将字符串 "2018" 连接到输入字符串后面。

为了给这个方法编写测试，我们需要编写一个函数，并用 `#[test]` 注解该函数。然后编译器就会知道这个函数是一个测试方法。Rust 中的 `assert_eq` 函数是一个宏。之后，第一个参数是函数的实际输出，在本例中是 `add_version` 函数，下一个参数是期望值，即 `String::from("abcd 2018.")`。

```
fn add_version(s: &str) -> String {
s.to_string() + " 2018."
}
#[test]
fn test_add_version() {
assert_eq!(add_version("abcd"), String::from("abcd 2018."));
}
```

如果写在 Cargo 包中，我们可以使用 `cargo test` 命令来编译并为我们运行测试。

```
$ cargo test
Compiling unittestingexample v0.1.0 (/path/to/code)
Finished dev [unoptimized + debuginfo] target(s) in 9.75s
Running target/debug/deps/unittestingexample-9d94da4358544e8a
running 1 test
test test_add_version ... ok
test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

请注意，会创建一个新的测试二进制文件，并且运行该测试二进制文件来评估测试用例。



## 1.13 本章小结

本章通过展示如何安装 Rust 和设置开发环境、如何使用 Cargo 启动 Rust 项目以及如何在 Rust 中使用变量和函数，向您介绍了 Rust 的基础知识。您还初步了解了 Rust 中的所有权和引用，以及如何使用结构体、枚举、特性和实现来实现一些面向对象范式。

在下一章中，您将学习如何使用 Rust 以及创建利用机器学习算法（如线性回归或逻辑回归）的 Rust 程序。

