---
layout: post
title:  Rust程序设计语言
date:   2018-10-29 23:04:30 +0800
categories: posts
---

最近在学 Rust，看的是 [Rust程序设计语言](https://kaisery.github.io/trpl-zh-cn/)。可以到 [playgroud](https://play.rust-lang.org/) 玩一下。

* 前言
  * 虽然不是那么明显，但 Rust 程序设计语言的本质在于 赋能（empowerment）：无论你现在编写的是何种代码，Rust 能让你在更为广泛的编程领域走得更远，写出自信。

* 入门指南
  * 安装
    * `rustup`
  * Hello, World!
    * `rustc main.rs && ./main`
    * main 函数是一个特殊的函数：在可执行的 Rust 程序中，它总是最先运行的代码。
    * rustfmt 会将代码格式化为特定的风格。
    * Rust 是一种 预编译静态类型（ahead-of-time compiled）语言，这意味着你可以编译程序，并将可执行文件送给其他人，他们甚至不需要安装 Rust 就可以运行。
  * Hello, Cargo!
    * Cargo 是 Rust 的构建系统和包管理器。
    * `cargo new hello_cargo --bin` 为 cargo new 传入 --bin 参数会生成一个可执行程序（通常就叫做 二进制文件，binary），而不是一个库。
    * `cargo build` 这个命令会创建一个可执行文件 target/debug/hello_cargo
    * `cargo run` 在一个命令中同时编译并运行生成的可执行文件
    * `cargo check` 快速检查代码确保其可以编译，但并不产生可执行文件
    * `cargo build --release` 优化编译项目

* 通用编程概念
  * 变量和可变性
    * 变量默认是不可改变的（immutable）。当变量不可变时，一旦值被绑定一个名称上，你就不能改变这个值。 `let x = 5;`
    * 变量只是默认不可变；正如在第二章所做的那样，你可以在变量名之前加 mut 来使其可变。除了允许改变值之外，mut 向读者表明了其他代码将会改变这个变量值的意图。`let mut x = 5;`
    * 除了防止出现 bug 外，还有很多地方需要权衡取舍。例如，使用大型数据结构时，适当地使用可变变量，可能比复制和返回新分配的实例更快。对于较小的数据结构，总是创建新实例，采用更偏向函数式的编程风格，可能会使代码更易理解，为可读性而牺牲性能或许是值得的。
    * 声明常量使用 const 关键字而不是 let，并且 必须 注明值的类型。`const MAX_POINTS: u32 = 100_000;`
    * 常量可以在任何作用域中声明，包括全局作用域。
    * 常量只能被设置为常量表达式，而不能是函数调用的结果，或任何其他只能在运行时计算出的值。
    * 我们可以定义一个与之前变量同名的新变量，而新变量会隐藏之前的变量。可以用相同变量名称来隐藏一个变量，以及重复使用 let 关键字来多次隐藏。
    * 隐藏与将变量标记为 mut 是有区别的。当不小心尝试对变量重新赋值时，如果没有使用 let 关键字，就会导致编译时错误。通过使用 let，我们可以用这个值进行一些计算，不过计算完之后变量仍然是不变的。
    * mut 与隐藏的另一个区别是，当再次使用 let 时，实际上创建了一个新变量，我们可以改变值的类型，但复用这个名字。`let spaces = "   ";
let spaces = spaces.len();`
  * 数据类型
    * 在 Rust 中，每一个值都属于某一个 数据类型（data type），这告诉 Rust 它被指定为何种数据，以便明确数据处理方式。我们将看到两类数据类型：标量（scalar）和复合（compound）。
    * 记住，Rust 是 静态类型（statically typed）语言，也就是说在编译时就必须知道所有变量的类型。根据值及其使用方式，编译器通常可以推断出我们想要用的类型。当多种类型均有可能时，比如第二章的 “比较猜测的数字和秘密数字” 使用 parse 将 String 转换为数字时，必须增加类型注解
    * 标量（scalar）类型代表一个单独的值。Rust 有四种基本的标量类型：整型、浮点型、布尔类型和字符类型。
      * 整型
        * 有符号整数类型以 i 开头而不是 u。
```
长度  有符号 无符号
8-bit i8  u8
16-bit  i16 u16
32-bit  i32 u32
64-bit  i64 u64
arch  isize usize
```
      * 浮点型
        * Rust 也有两个原生的 浮点数（floating-point numbers）类型，它们是带小数点的数字。Rust 的浮点数类型是 f32 和 f64，分别占 32 位和 64 位。
        * Rust 中的所有数字类型都支持基本数学运算：加法、减法、乘法、除法和取余。
      * 布尔型
        * Rust 中的布尔类型有两个可能的值：true 和 false。Rust 中的布尔类型使用 bool 表示。
      * 字符类型
        * Rust 的 char 类型是语言中最原生的字母类型。（注意 char 由单引号指定，不同于字符串使用双引号。）
        * Rust 的 char 类型代表了一个 Unicode 标量值（Unicode Scalar Value），这意味着它可以比 ASCII 表示更多内容。在 Rust 中，拼音字母（Accented letters），中文、日文、韩文等字符，emoji（绘文字）以及零长度的空白字符都是有效的 char 值。Unicode 标量值包含从 U+0000 到 U+D7FF 和 U+E000 到 U+10FFFF 在内的值。不过，“字符” 并不是一个 Unicode 中的概念，所以人直觉上的 “字符” 可能与 Rust 中的 char 并不符合。
    * 复合类型（Compound types）可以将多个值组合成一个类型。Rust 有两个原生的复合类型：元组（tuple）和数组（array）。
      * 元组类型
        * 元组是一个将多个其他类型的值组合进一个复合类型的主要方式。我们使用包含在圆括号中的逗号分隔的值列表来创建一个元组。元组中的每一个位置都有一个类型，而且这些不同值的类型也不必是相同的。`let tup: (i32, f64, u8) = (500, 6.4, 1);`
        * tup 变量绑定到整个元组上，因为元组是一个单独的复合元素。为了从元组中获取单个值，可以使用模式匹配（pattern matching）来解构（destructure）元组值。`let (x, y, z) = tup;` 程序首先创建了一个元组并绑定到 tup 变量上。接着使用了 let 和一个模式将 tup 分成了三个不同的变量，x、y 和 z。这叫做 解构（destructuring），因为它将一个元组拆成了三个部分。
        * 除了使用模式匹配解构外，也可以使用点号（.）后跟值的索引来直接访问它们。`let xx = tup.0`
      * 数组类型
        * 另一个包含多个值的方式是 数组（array）。与元组不同，数组中的每个元素的类型必须相同。Rust 中的数组与一些其他语言中的数组不同，因为 Rust 中的数组是固定长度的：一旦声明，它们的长度不能增长或缩小。`let a = [1, 2, 3, 4, 5];`
        * 数组是一整块分配在栈上的内存。可以使用索引来访问数组的元素 `let first = a[0];`
  * 函数
    * 函数遍布于 Rust 代码中。你已经见过语言中最重要的函数之一：main 函数，它是很多程序的入口点。你也见过 fn 关键字，它用来声明新函数。
    * Rust 代码中的函数和变量名使用 snake case 规范风格。在 snake case 中，所有字母都是小写并使用下划线分隔单词。
    ```rust
      fn main() {
          println!("Hello, world!");

          another_function();
      }

      fn another_function() {
          println!("Another function.");
      }
    ```
    * Rust 中的函数定义以 fn 开始并在函数名后跟一对圆括号。大括号告诉编译器哪里是函数体的开始和结尾。
    * Rust 不关心函数定义于何处，只要定义了就行。
    * 函数也可以被定义为拥有 参数（parameters），参数是特殊变量，是函数签名的一部分。当函数拥有参数（形参）时，可以为这些参数提供具体的值（实参）。技术上讲，这些具体值被称为参数（arguments），但是在闲谈时，人们倾向于互换着使用 parameter 和 argument 来表示函数定义中的变量或调用函数时传入的具体值。
    ```rust
      fn main() {
          another_function(5);
      }

      fn another_function(x: i32) {
          println!("The value of x is: {}", x);
      }
    ```
    * 在函数签名中，必须 声明每个参数的类型。这是 Rust 设计中一个经过慎重考虑的决定：要求在函数定义中提供类型注解，意味着编译器不需要你在代码的其他地方注明类型来指出你的意图。
    * 当一个函数有多个参数时，使用逗号分隔
    * 语句（Statements）是执行一些操作但不返回值的指令。表达式（Expressions）计算并产生一个值。
      * 使用 let 关键字创建变量并绑定一个值是一个语句。
      * 函数调用是一个表达式。宏调用是一个表达式。我们用来创建新作用域的大括号（代码块），{}，也是一个表达式
    * 表达式的结尾没有分号。如果在表达式的结尾加上分号，它就变成了语句，而语句不会返回值。
    ```rust
      fn main() {
          let x = 5;

          let y = {
              let x = 3;
              x + 1
          };

          println!("The value of y is: {}", y);
      }
    ```
    * 函数可以向调用它的代码返回值。我们并不对返回值命名，但要在箭头（->）后声明它的类型。在 Rust 中，函数的返回值等同于函数体最后一个表达式的值。使用 return 关键字和指定值，可从函数中提前返回；但大部分函数隐式的返回最后的表达式。
    ```rust
      fn five() -> i32 {
          5
      }

      fn main() {
          let x = five();

          println!("The value of x is: {}", x);
      }
    ```
  * 注释
    * 在 Rust 中，注释必须以两道斜杠开始，并持续到本行的结尾。对于超过一行的注释，需要在每一行前都加上 //。
    * 注释也可以在放在包含代码的行的末尾
  * 控制流
    * Rust 代码中最常见的用来控制执行流的结构是 if 表达式和循环。
    * if 表达式允许根据条件执行不同的代码分支。你提供一个条件并表示 “如果条件满足，运行这段代码；如果条件不满足，不运行这段代码。”
    ```rust
      fn main() {
          let number = 3;

          if number < 5 {
              println!("condition was true");
          } else {
              println!("condition was false");
          }
      }
    ```
    * 另外值得注意的是代码中的条件 必须 是 bool 值。如果条件不是 bool 值，我们将得到一个错误。
    * 不像 Ruby 或 JavaScript 这样的语言，Rust 并不会尝试自动地将非布尔值转换为布尔值。必须总是显式地使用布尔值作为 if 的条件。
    * 可以将 else if 表达式与 if 和 else 组合来实现多重条件。
    * 因为 if 是一个表达式，我们可以在 let 语句的右侧使用它
    * 代码块的值是其最后一个表达式的值，而数字本身就是一个表达式。
    * Rust 有三种循环：loop、while 和 for。
    * loop 关键字告诉 Rust 一遍又一遍地执行一段代码直到你明确要求停止。
    ```rust
      fn main() {
          loop {
              println!("again!");
          }
      }
    ```
    * 可以使用 break 关键字来告诉程序何时停止循环。
    * 当条件为真，执行循环。这个模式太常用了，Rust 为此内置了一个语言结构，它被称为 while 循环。
    ```rust
      fn main() {
          let mut number = 3;

          while number != 0 {
              println!("{}!", number);

              number = number - 1;
          }

          println!("LIFTOFF!!!");
      }
    ```
    * 可以使用 for 循环来对一个集合的每个元素执行一些代码。
    ```rust
      fn main() {
          let a = [10, 20, 30, 40, 50];

          for element in a.iter() {
              println!("the value is: {}", element);
          }

          for number in (1..4).rev() {
              println!("{}!", number);
          }
          println!("LIFTOFF!!!");
      }
    ```
* 认识所有权
  * 所有权（系统）是 Rust 最独特的功能，其令 Rust 无需垃圾回收（garbage collector）即可保障内存安全。
  * 通过所有权系统管理内存，编译器在编译时会根据一系列的规则进行检查。在运行时，所有权系统的任何功能都不会减慢程序。
  * 所有权规则
    * Rust 中的每一个值都有一个被称为其 所有者（owner）的变量。
    * 值有且只有一个所有者。
    * 当所有者（变量）离开作用域，这个值将被丢弃。
  * 作用域是一个项（item）在程序中有效的范围。
  ```rust
    fn main() {
        {                      // s 在这里无效, 它尚未声明
            let s = "hello";   // 从此处起，s 是有效的

            // 使用 s
        }                      // 此作用域已结束，s 不再有效
    }
  ```
  * 变量与数据交互的方式
    * 移动
    * 克隆
  * Rust 有一个叫做 Copy trait 的特殊注解，可以用在类似整型这样的存储在栈上的类型上（第十章详细讲解 trait）。如果一个类型拥有 Copy trait，一个旧的变量在将其赋值给其他变量后仍然可用。Rust 不允许自身或其任何部分实现了 Drop trait 的类型使用 Copy trait。如果我们对其值离开作用域时需要特殊处理的类型使用 Copy 注解，将会出现一个编译时错误。要学习如何为你的类型增加 Copy 注解，请阅读附录 C 中的 “可派生的 trait”。
  * 变量的所有权总是遵循相同的模式：将值赋给另一个变量时移动它。当持有堆中数据值的变量离开作用域时，其值将通过 drop 被清理掉，除非数据被移动为另一个变量所有。
  * & 符号就是 引用，它们允许你使用值但不获取其所有权。
  * 与使用 & 引用相反的操作是 解引用（dereferencing），它使用解引用运算符，*。
  * &s1 语法让我们创建一个 指向 值 s1 的引用，但是并不拥有它。因为并不拥有这个值，当引用离开作用域时其指向的值也不会被丢弃。
  * 我们将获取引用作为函数参数称为 借用（borrowing）。
  *（默认）不允许修改引用的值。
  * 可变引用 &mut s
  ```rust
    fn main() {
        let mut s = String::from("hello");

        change(&mut s);
    }

    fn change(some_string: &mut String) {
        some_string.push_str(", world");
    }
  ```
  * 不过可变引用有一个很大的限制：在特定作用域中的特定数据有且只有一个可变引用。
  * 引用的规则
    * 在任意给定时间，要么 只能有一个可变引用，要么 只能有多个不可变引用。
    * 引用必须总是有效。
  * 另一个没有所有权的数据类型是 slice。slice 允许你引用集合中一段连续的元素序列，而不用引用整个集合。
  * 字符串 slice（string slice）是 String 中一部分值的引用
  ```rust
    fn main() {
        let s = String::from("hello world");

        let hello = &s[0..5];
        let world = &s[6..11];
    }
  ```
  * 字符串字面值就是 slice
* 使用结构体组织相关联的数据
  * struct，或者 structure，是一个自定义数据类型，允许你命名和包装多个相关的值，从而形成一个有意义的组合。
  * 定义结构体，需要使用 struct 关键字并为整个结构体提供一个名字。结构体的名字需要描述它所组合的数据的意义。接着，在大括号中，定义每一部分数据的名字和类型，我们称为 字段（field）。
  ```rust
    fn main() {
        struct User {
            username: String,
            email: String,
            sign_in_count: u64,
            active: bool,
        }
    }
  ```
  * 一旦定义了结构体后，为了使用它，通过为每个字段指定具体值来创建这个结构体的 实例。创建一个实例需要以结构体的名字开头，接着在大括号中使用 key: value 键-值对的形式提供字段，其中 key 是字段的名字，value 是需要存储在字段中的数据值。实例中字段的顺序不需要和它们在结构体中声明的顺序一致。换句话说，结构体的定义就像一个类型的通用模板，而实例则会在这个模板中放入特定数据来创建这个类型的值。
  ```rust
    let user1 = User {
        email: String::from("someone@example.com"),
        username: String::from("someusername123"),
        active: true,
        sign_in_count: 1,
    };
  ```
  * 为了从结构体中获取某个特定的值，可以使用点号。如果我们只想要用户的邮箱地址，可以用 user1.email。要更改结构体中的值，如果结构体的实例是可变的，我们可以使用点号并为对应的字段赋值。
  ```rust
    let mut user1 = User {
        email: String::from("someone@example.com"),
        username: String::from("someusername123"),
        active: true,
        sign_in_count: 1,
    };
    user1.email = String::from("anotheremail@example.com");
  ```
  * 注意整个实例必须是可变的；Rust 并不允许只将某个字段标记为可变。另外需要注意同其他任何表达式一样，我们可以在函数体的最后一个表达式中构造一个结构体的新实例，来隐式地返回这个实例。
  * 字段初始化简写语法
  ```rust
    fn build_user(email: String, username: String) -> User {
        User {
            email, //same as email: email,
            username, //same as username: username,
            active: true,
            sign_in_count: 1,
        }
    }
  ```
  * 结构体更新语法（struct update syntax）
  ```rust
    let user2 = User {
        email: String::from("another@example.com"),
        username: String::from("anotherusername567"),
        ..user1
        // same as
        // active: user1.active,
        // sign_in_count: user1.sign_in_count,
    };
  ```
  * 元组结构体有着结构体名称提供的含义，但没有具体的字段名，只有字段的类型。当你想给整个元组取一个名字，并使元组成为与其他元组不同的类型时，元组结构体是很有用的，这时像常规结构体那样为每个字段命名就显得多余和形式化了。
  ```rust
    #![allow(unused_variables)]
    fn main() {
        struct Color(i32, i32, i32);
        struct Point(i32, i32, i32);

        let black = Color(0, 0, 0);
        let origin = Point(0, 0, 0);
    }
  ```
  * 我们也可以定义一个没有任何字段的结构体！它们被称为 类单元结构体（unit-like structs）因为它们类似于 ()，即 unit 类型。类单元结构体常常在你想要在某个类型上实现 trait 但不需要在类型中存储数据的时候发挥作用。
  * 方法 与函数类似：它们使用 fn 关键字和名称声明，可以拥有参数和返回值，同时包含在某处调用该方法时会执行的代码。不过方法与函数是不同的，因为它们在结构体的上下文中被定义（或者是枚举或 trait 对象的上下文，将分别在第六章和第十七章讲解），并且它们第一个参数总是 self，它代表调用该方法的结构体实例。
  ```rust
    #[derive(Debug)]
    struct Rectangle {
        width: u32,
        height: u32,
    }

    // 为了使函数定义于 Rectangle 的上下文中，我们开始了一个 impl 块（impl 是 implementation 的缩写）。接着将 area 函数移动到 impl 大括号中，并将签名中的第一个（在这里也是唯一一个）参数和函数体中其他地方的对应参数改成 self。然后在 main 中将我们先前调用 area 方法并传递 rect1 作为参数的地方，改成使用 方法语法（method syntax）在 Rectangle 实例上调用 area 方法。方法语法获取一个实例并加上一个点号，后跟方法名、圆括号以及任何参数。
    impl Rectangle {
        //在 area 的签名中，使用 &self 来替代 rectangle: &Rectangle，因为该方法位于 impl Rectangle 上下文中所以 Rust 知道 self 的类型是 Rectangle。注意仍然需要在 self 前面加上 &，就像 &Rectangle 一样。方法可以选择获取 self 的所有权，或者像我们这里一样不可变地借用 self，或者可变地借用 self，就跟其他参数一样。
        fn area(&self) -> u32 {
            self.width * self.height
        }
    }

    fn main() {
        let rect1 = Rectangle { width: 30, height: 50 };

        println!(
            "The area of the rectangle is {} square pixels.",
            rect1.area()
        );
    }
  ```
  * 使用方法替代函数，除了可使用方法语法和不需要在每个函数签名中重复 self 的类型之外，其主要好处在于组织性。我们将某个类型实例能做的所有事情都一起放入 impl 块中，而不是让将来的用户在我们的库中到处寻找 Rectangle 的功能。
  * impl 块的另一个有用的功能是：允许在 impl 块中定义 不 以 self 作为参数的函数。这被称为 关联函数（associated functions），因为它们与结构体相关联。它们仍是函数而不是方法，因为它们并不作用于一个结构体的实例。你已经使用过 String::from 关联函数了。
  ```rust
    impl Rectangle {
        fn square(size: u32) -> Rectangle {
            Rectangle { width: size, height: size }
        }
    }
  ```
  * 每个结构体都允许拥有多个 impl 块。
* 枚举与模式匹配
  * 枚举允许你通过列举可能的值来定义一个类型。
  * 可以通过在代码中定义一个 IpAddrKind 枚举来表现这个概念并列出可能的 IP 地址类型，V4 和 V6。这被称为枚举的 成员（variants）
  ```rust
    enum IpAddrKind {
        V4,
        V6,
    }

    let four = IpAddrKind::V4;
    let six = IpAddrKind::V6;

    fn route(ip_type: IpAddrKind) { }
  ```
  * 我们可以使用一种更简洁的方式来表达相同的概念，仅仅使用枚举并将数据直接放进每一个枚举成员而不是将枚举作为结构体的一部分。
  ```rust
    enum IpAddr {
        V4(String),
        V6(String),
    }

    let home = IpAddr::V4(String::from("127.0.0.1"));

    let loopback = IpAddr::V6(String::from("::1"));
  ```
  * 结构体和枚举还有另一个相似点：就像可以使用 impl 来为结构体定义方法那样，也可以在枚举上定义方法。
  ```rust
    impl Message {
        fn call(&self) {
            // 在这里定义方法体
        }
    }

    let m = Message::Write(String::from("hello"));
    m.call();
  ```
  * Rust 并没有空值，不过它确实拥有一个可以编码存在或不存在概念的枚举。这个枚举是 Option<T>，而且它定义于标准库中
  ```rust
    enum Option<T> {
        Some(T),
        None,
    }

    let some_number = Some(5);
    let some_string = Some("a string");

    let absent_number: Option<i32> = None;
  ```
  * 如果使用 None 而不是 Some，需要告诉 Rust Option<T> 是什么类型的，因为编译器只通过 None 值无法推断出 Some 成员保存的值的类型。
  * Rust 有一个叫做 match 的极为强大的控制流运算符，它允许我们将一个值与一系列的模式相比较并根据相匹配的模式执行相应代码。模式可由字面值、变量、通配符和许多其他内容构成；第十八章会涉及到所有不同种类的模式以及它们的作用。match 的力量来源于模式的表现力以及编译器检查，它确保了所有可能的情况都得到处理。
  ```rust
    enum Coin {
        Penny,
        Nickel,
        Dime,
        Quarter,
    }

    fn value_in_cents(coin: Coin) -> u32 {
        match coin {
            Coin::Penny => 1,
            Coin::Nickel => 5,
            Coin::Dime => 10,
            Coin::Quarter => 25,
        }
    }
  ```
  * 将 match 与枚举相结合在很多场景中都是有用的。你会在 Rust 代码中看到很多这样的模式：match 一个枚举，绑定其中的值到一个变量，接着根据其值执行代码。
  * Rust 知道我们没有覆盖所有可能的情况甚至知道哪些模式被忘记了！Rust 中的匹配是 穷尽的（exhaustive）：必须穷举到最后的可能性来使代码有效。
  * Rust 也提供了一个模式用于不想列举出所有可能值的场景: 可以使用特殊的模式 _ 替代。
  * if let 语法让我们以一种不那么冗长的方式结合 if 和 let，来处理只匹配一个模式的值而忽略其他模式的情况。
  ```rust
    let mut count = 0;
    if let Coin::Quarter(state) = coin {
        println!("State quarter from {:?}!", state);
    } else {
        count += 1;
    }
  ```
* 模块
  * 模块（module）是一个包含函数或类型定义的命名空间，你可以选择这些定义能（公有）或不能（私有）在其模块外可见。
    * 使用 mod 关键字声明新模块。此模块中的代码要么直接位于声明之后的大括号中，要么位于另一个文件。
    * 函数、类型、常量和模块默认都是私有的。可以使用 pub 关键字将其变成公有并在其命名空间之外可见。
    * use 关键字将模块或模块中的定义引入到作用域中以便于引用它们。
    * `cargo new communicator --lib`
    * mod 关键字的后面是模块的名字，network，接着是位于大括号中的代码块。代码块中的一切都位于 network 命名空间中。在这个例子中，只有一个函数，connect。如果想要在 network 模块外面的代码中调用这个函数，需要指定模块名并使用命名空间语法 ::，像这样：network::connect()。
    ```rust
      mod network {
          fn connect() {
          }
      }
    ```
    * Rust 默认只知道 src/lib.rs 中的内容。如果想要对项目加入更多文件，我们需要在 src/lib.rs 中告诉 Rust 去寻找其他文件；这就是为什么 mod client 需要被定义在 src/lib.rs 而不能在 src/client.rs 的原因。
    * 模块文件系统的规则
      * 如果一个叫做 foo 的模块没有子模块，应该将 foo 的声明放入叫做 foo.rs 的文件中。
      * 如果一个叫做 foo 的模块有子模块，应该将 foo 的声明放入叫做 foo/mod.rs 的文件中。
    * 使用 extern crate 指令将 communicator 库 crate 引入到作用域。我们的包现在包含 两个 crate。Cargo 认为 src/main.rs 是一个二进制 crate 的根文件，与现存的以 src/lib.rs 为根文件的库 crate 相区分。这个模式在可执行项目中非常常见：大部分功能位于库 crate 中，而二进制 crate 使用该库 crate。通过这种方式，其他程序也可以使用这个库 crate，这是一个很好的关注分离（separation of concerns）。
    ```rust
      extern crate communicator;

      fn main() {
          communicator::client::connect();
      }
    ```
    * Rust 所有代码的默认状态是私有的：除了自己之外别人不允许使用这些代码。如果不在自己的项目中使用一个私有函数，因为程序自身是唯一允许使用这个函数的代码，Rust 会警告说函数未被使用。
    * 为了告诉 Rust 将函数标记为公有，在声明的开头增加 pub 关键字。
    ```rust
      pub mod client;

      mod network;

      pub fn connect() {
      }
    ```
    * 私有性规则
      * 如果一个项是公有的，它能被任何父模块访问
      * 如果一个项是私有的，它能被其直接父模块及其任何子模块访问
    * Rust 的 use 关键字能将你想要调用的函数所在的模块引入到当前作用域中，通过这种方式可以缩短冗长的函数调用。
    ```rust
      pub mod a {
          pub mod series {
              pub mod of {
                  pub fn nested_modules() {}
              }
          }
      }

      use a::series::of;

      fn main() {
          of::nested_modules();
      }
    ```
    * use 关键字只将指定的模块引入作用域；它并不会将其子模块也引入。
    * 因为枚举也像模块一样组成了某种命名空间，也可以使用 use 来导入枚举的成员。对于任何类型的 use 语句，如果从一个命名空间导入多个项，可以在最后使用大括号和逗号来列举它们
    ```rust
      enum TrafficLight {
          Red,
          Yellow,
          Green,
      }

      use TrafficLight::{Red, Yellow};

      fn main() {
          let red = Red;
          let yellow = Yellow;
          let green = TrafficLight::Green;
      }
    ```
    * 为了一次将某个命名空间下的所有名称都引入作用域，可以使用 * 语法，这称为 glob 运算符（glob operator）。
    ```rust
      enum TrafficLight {
          Red,
          Yellow,
          Green,
      }

      use TrafficLight::*;

      fn main() {
          let red = Red;
          let yellow = Yellow;
          let green = Green;
      }
    ```
    * 可以使用 super 在层级中上移到当前模块的上一级模块
* 通用集合类型
  * Rust 标准库中包含一系列被称为 集合（collections）的非常有用的数据结构。大部分其他数据类型都代表一个特定的值，不过集合可以包含多个值。不同于内建的数组和元组类型，这些集合指向的数据是储存在堆上的，这意味着数据的数量不必在编译时就已知并且可以随着程序的运行增长或缩小。
  * vector 允许我们在一个单独的数据结构中储存多于一个的值，它在内存中彼此相邻地排列所有的值。vector 只能储存相同类型的值。
  ```rust
    let v: Vec<i32> = Vec::new();
    let v = vec![1, 2, 3];

    // 新建一个 vector 并向其增加元素，可以使用 push 方法
    let mut v = Vec::new();
    v.push(5);
  ```
  * 类似于任何其他的 struct，vector 在其离开作用域时会被释放
  * 访问 vector 中一个值的两种方式，索引语法或者 get 方法
  ```rust
    let v = vec![1, 2, 3, 4, 5];

    // 当引用一个不存在的元素时 Rust 会造成 panic!
    let third: &i32 = &v[2];
    // 当 get 方法被传递了一个数组外的索引时，它不会 panic 而是返回 None。
    let third: Option<&i32> = v.get(2);

    // 使用 for 循环来获取 i32 值的 vector 中的每一个元素的不可变引用并将其打印
    let v = vec![100, 32, 57];
    for i in &v {
        println!("{}", i);
    }

    // 遍历可变 vector 的每一个元素的可变引用以便能改变他们
    let mut v = vec![100, 32, 57];
    for i in &mut v {
        *i += 50;
    }
  ```
  * 当需要在 vector 中储存不同类型值时，我们可以定义并使用一个枚举！
  * Rust 的核心语言中只有一种字符串类型：str，字符串 slice，它通常以被借用的形式出现，&str。
  * String 的类型是由标准库提供的，而没有写进核心语言部分，它是可增长的、可变的、有所有权的、UTF-8 编码的字符串类型。
  ```rust
    let mut s = String::new();

    // to_string
    let data = "initial contents";
    let s = data.to_string();

    // String::from
    let s = String::from("initial contents");

    // 通过 push_str 方法来附加字符串 slice，从而使 String 变长
    let mut s = String::from("foo");
    s.push_str("bar");

    // push 方法被定义为获取一个单独的字符作为参数，并附加到 String 中
    let mut s = String::from("lo");
    s.push('l');

    // 通常你会希望将两个已知的字符串合并在一起。一种办法是像这样使用 + 运算符
    let s1 = String::from("Hello, ");
    let s2 = String::from("world!");
    let s3 = s1 + &s2; // note s1 has been moved here and can no longer be used

    // 对于更为复杂的字符串链接，可以使用 format! 宏
    let s1 = String::from("tic");
    let s2 = String::from("tac");
    let s3 = String::from("toe");
    let s = format!("{}-{}-{}", s1, s2, s3);

    // 可以使用 [] 和一个 range 来创建含特定字节的字符串 slice
    let hello = "Здравствуйте";
    let s = &hello[0..4];

    // 如果你需要操作单独的 Unicode 标量值，最好的选择是使用 chars 方法。
    for c in "नमस्ते".chars() {
        println!("{}", c);
    }
    // bytes 方法返回每一个原始字节
    for b in "नमस्ते".bytes() {
        println!("{}", b);
    }
  ```
  * HashMap<K, V> 类型储存了一个键类型 K 对应一个值类型 V 的映射。它通过一个 哈希函数（hashing function）来实现映射，决定如何将键和值放入内存中。很多编程语言支持这种数据结构，不过通常有不同的名字：哈希、map、对象、哈希表或者关联数组，仅举几例。
  ```rust
    use std::collections::HashMap;

    // 可以使用 new 创建一个空的 HashMap，并使用 insert 增加元素
    let mut scores = HashMap::new();
    scores.insert(String::from("Blue"), 10);
    scores.insert(String::from("Yellow"), 50);

    // 另一个构建哈希 map 的方法是使用一个元组的 vector 的 collect 方法，其中每个元组包含一个键值对。
    let teams  = vec![String::from("Blue"), String::from("Yellow")];
    let initial_scores = vec![10, 50];
    let scores: HashMap<_, _> = teams.iter().zip(initial_scores.iter()).collect();

    // 可以通过 get 方法并提供对应的键来从哈希 map 中获取值
    let mut scores = HashMap::new();
    scores.insert(String::from("Blue"), 10);
    scores.insert(String::from("Yellow"), 50);
    let team_name = String::from("Blue");
    let score = scores.get(&team_name);

    // 可以使用与 vector 类似的方式来遍历哈希 map 中的每一个键值对，也就是 for 循环
    for (key, value) in &scores {
        println!("{}: {}", key, value);
    }

    // 覆盖一个值，调用了两次 insert，哈希 map 也只会包含一个键值对
    let mut scores = HashMap::new();
    scores.insert(String::from("Blue"), 10);
    scores.insert(String::from("Blue"), 25);

    // 只在键没有对应值时插入
    // 我们经常会检查某个特定的键是否有值，如果没有就插入一个值。为此哈希 map 有一个特有的 API，叫做 entry，它获取我们想要检查的键作为参数。entry 函数的返回值是一个枚举，Entry，它代表了可能存在也可能不存在的值。
    let mut scores = HashMap::new();
    scores.insert(String::from("Blue"), 10);
    scores.entry(String::from("Yellow")).or_insert(50); // Entry 的 or_insert 方法在键对应的值存在时就返回这个值的 Entry，如果不存在则将参数作为新值插入并返回修改过的 Entry。
    scores.entry(String::from("Blue")).or_insert(50);
    println!("{:?}", scores);

    // 找到一个键对应的值并根据旧的值更新它
  ```
* 错误处理
  * Rust 将错误组合成两个主要类别：可恢复错误（recoverable）和 不可恢复错误（unrecoverable）。可恢复错误通常代表向用户报告错误和重试操作是合理的情况，比如未找到文件。不可恢复错误通常是 bug 的同义词，比如尝试访问超过数组结尾的位置。
  * Rust 有 panic!宏。当执行这个宏时，程序会打印出一个错误信息，展开并清理栈数据，然后接着退出。
  * 当出现 panic! 时，程序默认会开始 展开（unwinding），这意味着 Rust 会回溯栈并清理它遇到的每一个函数的数据，不过这个回溯并清理的过程有很多工作。另一种选择是直接 终止（abort），这会不清理数据就退出程序。那么程序所使用的内存需要由操作系统来清理。如果你需要项目的最终二进制文件越小越好，panic 时通过在 Cargo.toml 的 [profile] 部分增加 panic = 'abort'，可以由展开切换为终止
  * unwrap，它的实现就类似于示例 9-4 中的 match 语句。如果 Result 值是成员 Ok，unwrap 会返回 Ok 中的值。如果 Result 是成员 Err，unwrap 会为我们调用 panic!。
  ```rust
    use std::fs::File;

    fn main() {
        let f = File::open("hello.txt").unwrap();
    }
  ```
  * 使用 expect 而不是 unwrap 并提供一个好的错误信息可以表明你的意图并更易于追踪 panic 的根源。
  ```rust
    use std::fs::File;

    fn main() {
        let f = File::open("hello.txt").expect("Failed to open hello.txt");
    }
  ```
  * 使用 ? 向调用者返回错误
  ```rust
    use std::io;
    use std::io::Read;
    use std::fs::File;

    fn read_username_from_file() -> Result<String, io::Error> {
        let mut f = File::open("hello.txt")?;
        let mut s = String::new();
        f.read_to_string(&mut s)?;
        Ok(s)
    }
  ```
  * ? 只能被用于返回值类型为 Result 的函数
  * 如果代码 panic，就没有恢复的可能。你可以选择对任何错误场景都调用 panic!，不管是否有可能恢复，不过这样就是你代替调用者决定了这是不可恢复的。选择返回 Result 值的话，就将选择权交给了调用者，而不是代替他们做出决定。调用者可能会选择以符合他们场景的方式尝试恢复，或者也可能干脆就认为 Err 是不可恢复的，所以他们也可能会调用 panic! 并将可恢复的错误变成了不可恢复的错误。因此返回 Result 是定义可能会失败的函数的一个好的默认选择。
* 泛型、trait 和生命周期
  * 泛型是具体类型或其他属性的抽象替代。我们可以表达泛型的属性，比如他们的行为或如何与其他泛型相关联，而不需要在编写和编译代码时知道他们在这里实际上代表什么。
  * trait，这是一个定义泛型行为的方法。trait 可以与泛型结合来将泛型限制为拥有特定行为的类型，而不是任意类型。
  * 生命周期（lifetimes），它是一类允许我们向编译器提供引用如何相互关联的泛型。Rust 的生命周期功能允许在很多场景下借用值的同时仍然使编译器能够检查这些引用的有效性。
  * 泛型用于为函数签名或结构体等创建定义，允许我们创建许多不同的具体数据类型。
  ```rust
    // 在函数定义中使用泛型
    // 函数 largest 有泛型类型 T。它有一个参数 list，它的类型是一个 T 值的 slice。largest 函数将会返回一个与 T 相同类型的值。
    fn largest<T>(list: &[T]) -> T {
    }

    // 结构体定义中的泛型
    // 使用 <> 语法来定义拥有一个或多个泛型参数类型字段的结构体
    struct Point<T> {
        x: T,
        y: T,
    }
    fn main() {
        let integer = Point { x: 5, y: 10 };
        let float = Point { x: 1.0, y: 4.0 };
    }

    // 枚举定义中的泛型数据类型
    enum Option<T> {
        Some(T),
        None,
    }

    // 方法定义中的枚举数据类型
    struct Point<T> {
        x: T,
        y: T,
    }
    impl<T> Point<T> {
        fn x(&self) -> &T {
            &self.x
        }
    }
    fn main() {
        let p = Point { x: 5, y: 10 };

        println!("p.x = {}", p.x());
    }
  ```
  * trait 允许我们进行另一种抽象：他们让我们可以抽象类型所通用的行为。trait 告诉 Rust 编译器某个特定类型拥有可能与其他类型共享的功能。在使用泛型类型参数的场景中，可以使用 trait bounds 在编译时指定泛型可以是任何实现了某个 trait 的类型，并由此在这个场景下拥有我们希望的功能。
  ```rust
    // 使用 trait 关键字来声明一个 trait，后面是 trait 的名字，在这个例子中是 Summarizable。在大括号中声明描述实现这个 trait 的类型所需要的行为的方法签名，在这个例子中是 fn summary(&self) -> String。在方法签名后跟分号，而不是在大括号中提供其实现。接着每一个实现这个 trait 的类型都需要提供其自定义行为的方法体，编译器也会确保任何实现 Summarizable trait 的类型都拥有与这个签名的定义完全一致的 summary 方法。
    pub trait Summarizable {
        fn summary(&self) -> String;
    }

    // 为类型实现 trait
    pub struct NewsArticle {
        pub headline: String,
        pub location: String,
        pub author: String,
        pub content: String,
    }
    impl Summarizable for NewsArticle {
        fn summary(&self) -> String {
            format!("{}, by {} ({})", self.headline, self.author, self.location)
        }
    }
    pub struct Tweet {
        pub username: String,
        pub content: String,
        pub reply: bool,
        pub retweet: bool,
    }
    impl Summarizable for Tweet {
        fn summary(&self) -> String {
            format!("{}: {}", self.username, self.content)
        }
    }

    // 默认实现
    // 默认实现允许调用相同 trait 中的其他方法，哪怕这些方法没有默认实现。
    pub trait Summarizable {
        fn summary(&self) -> String {
            String::from("(Read more...)")
        }
    }
  ```
  * trait 实现的一个需要注意的限制是：只能在 trait 或对应类型位于我们 crate 本地的时候为其实现 trait。换句话说，不允许对外部类型实现外部 trait。例如，不能在 Vec 上实现 Display trait，因为 Display 和 Vec 都定义于标准库中。允许在像 Tweet 这样作为我们 aggregatorcrate 部分功能的自定义类型上实现标准库中的 trait Display。也允许在 aggregatorcrate 中为 Vec 实现 Summarizable，因为 Summarizable 定义于此。
  * 现在我们定义了 trait 并在类型上实现了这些 trait，也可以对泛型类型参数使用 trait。我们可以限制泛型不再适用于任何类型，编译器会确保其被限制为那些实现了特定 trait 的类型，由此泛型就会拥有我们希望其类型所拥有的功能。这被称为指定泛型的 trait bounds。
  ```rust
    pub fn notify<T: Summarizable>(item: T) {
        println!("Breaking news! {}", item.summary());
    }

    // 可以通过 + 来为泛型指定多个 trait bounds
    fn some_function<T: Display + Clone, U: Clone + Debug>(t: T, u: U) -> i32 {}

    // 也可以使用 where 从句
    fn some_function<T, U>(t: T, u: U) -> i32
        where T: Display + Clone,
              U: Clone + Debug
    {
    }
  ```
  * 通过使用带有 trait bound 的泛型 impl 块，可以有条件的只为实现了特定 trait 的类型实现方法。
  * 也可以对任何实现了特定 trait 的类型有条件的实现 trait。对任何满足特定 trait bound 的类型实现 trait 被称为 blanket implementations，他们被广泛的用于 Rust 标准库中。
  * 生命周期的主要目标是避免悬垂引用，它会导致程序引用了非预期引用的数据。
  * 生命周期注解并不改变任何引用的生命周期的长短。与当函数签名中指定了泛型类型参数后就可以接受任何类型一样，当指定了泛型生命周期后函数也能接受任何生命周期的引用。生命周期注解所做的就是将多个引用的生命周期联系起来。
  ```rust
    &i32        // a reference
    &'a i32     // a reference with an explicit lifetime
    &'a mut i32 // a mutable reference with an explicit lifetime

    // 函数签名中的生命周期注解
    fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
        if x.len() > y.len() {
            x
        } else {
            y
        }
    }
  ```
  * 也可以定义存放引用的结构体，不过需要为结构体定义中的每一个引用添加生命周期注解。
  ```rust
    struct ImportantExcerpt<'a> {
        part: &'a str,
    }

    fn main() {
        let novel = String::from("Call me Ishmael. Some years ago...");
        let first_sentence = novel.split('.')
            .next()
            .expect("Could not find a '.'");
        let i = ImportantExcerpt { part: first_sentence };
    }
  ```
  * 被编码进 Rust 引用分析的模式被称为 生命周期省略规则（lifetime elision rules）。这并不是需要程序员遵守的规则；这些规则是一系列特定的场景，此时编译器会考虑，如果代码符合这些场景，就无需明确指定生命周期。
  * 函数或方法的参数的生命周期被称为 输入生命周期（input lifetimes），而返回值的生命周期被称为 输出生命周期（output lifetimes）。
  * 现在介绍编译器用于判断引用何时不需要明确生命周期注解的规则。第一条规则适用于输入生命周期，后两条规则适用于输出生命周期。如果编译器检查完这三条规则后仍然存在没有计算出生命周期的引用，编译器将会停止并生成错误。
    * 每一个是引用的参数都有它自己的生命周期参数。换句话说就是，有一个引用参数的函数有一个生命周期参数：fn foo<'a>(x: &'a i32)，有两个引用参数的函数有两个不同的生命周期参数，fn foo<'a, 'b>(x: &'a i32, y: &'b i32)，依此类推。
    * 如果只有一个输入生命周期参数，那么它被赋予所有输出生命周期参数：fn foo<'a>(x: &'a i32) -> &'a i32。
    * 如果方法有多个输入生命周期参数，不过其中之一因为方法的缘故为 &self 或 &mut self，那么 self 的生命周期被赋给所有输出生命周期参数。这使得方法编写起来更简洁。
  * 'static 生命周期存活于整个程序期间。所有的字符串字面值都拥有 'static 生命周期
* 测试
  * Rust 中的测试函数是用来验证非测试代码是否按照期望的方式运行的。测试函数体通常执行如下三种操作：
    * 设置任何所需的数据或状态
    * 运行需要测试的代码
    * 断言其结果是我们所期望的
  * assert! 宏由标准库提供，在希望确保测试中一些条件为 true 时非常有用。需要向 assert! 宏提供一个求值为布尔值的参数。如果值是 true，assert! 什么也不做，同时测试会通过。如果值为 false，assert! 调用 panic! 宏，这会导致测试失败。assert! 宏帮助我们检查代码是否以期望的方式运行。
  * assert_eq! 和 assert_ne!。这两个宏分别比较两个值是相等还是不相等。当断言失败时他们也会打印出这两个值具体是什么，以便于观察测试 为什么 失败，而 assert! 只会打印出它从 == 表达式中得到了 false 值，而不是导致 false 的两个值。
  * assert_eq! 和 assert_ne! 宏在底层分别使用了 == 和 !=。当断言失败时，这些宏会使用调试格式打印出其参数，这意味着被比较的值必需实现了 PartialEq 和 Debug trait。所有的基本类型和大部分标准库类型都实现了这些 trait。对于自定义的结构体和枚举，需要实现 PartialEq 才能断言他们的值是否相等。需要实现 Debug 才能在断言失败时打印他们的值。
  * 可以通过对函数增加另一个属性 should_panic 来实现这些。这个属性在函数中的代码 panic 时会通过，而在其中的代码没有 panic 时失败。
  * cargo test 在测试模式下编译代码并运行生成的测试二进制文件。可以指定命令行参数来改变 cargo test 的默认行为。例如，cargo test 生成的二进制文件的默认行为是并行的运行所有测试，并截获测试运行过程中产生的输出，阻止他们被显示出来，使得阅读测试结果相关的内容变得更容易。
  * Rust 社区倾向于根据测试的两个主要分类来考虑问题：单元测试（unit tests）与 集成测试（integration tests）。单元测试倾向于更小而更集中，在隔离的环境中一次测试一个模块，或者是测试私有接口。而集成测试对于你的库来说则完全是外部的。它们与其他外部代码一样，通过相同的方式使用你的代码，只测试公有接口而且每个测试都有可能会测试多个模块。
  * 单元测试的目的是在与其他部分隔离的环境中测试每一个单元的代码，以便于快速而准确的某个单元的代码功能是否符合预期。单元测试与他们要测试的代码共同存放在位于 src 目录下相同的文件中。规范是在每个文件中创建包含测试函数的 tests 模块，并使用 cfg(test) 标注模块。
  ```rust
    #[cfg(test)]
    mod tests {
        #[test]
        fn it_works() {
            assert_eq!(2 + 2, 4);
        }
    }
  ```
  * 在 Rust 中，集成测试对于你需要测试的库来说完全是外部的。同其他使用库的代码一样使用库文件，也就是说它们只能调用一部分库中的公有 API 。集成测试的目的是测试库的多个部分能否一起正常工作。一些单独能正确运行的代码单元集成在一起也可能会出现问题，所以集成测试的覆盖率也是很重要的。为了创建集成测试，你需要先创建一个 tests 目录。
  ```rust
    extern crate adder;

    #[test]
    fn it_adds_two() {
        assert_eq!(4, adder::add_two(2));
    }
  ```
* 一个 I/O 项目：构建一个命令行程序
  * 二进制项目的关注分离
    * main 函数负责多个任务的组织问题在许多二进制项目中很常见。所以 Rust 社区开发出一类在 main 函数开始变得庞大时进行二进制程序的关注分离的指导性过程。这些过程有如下步骤：
      * 将程序拆分成 main.rs 和 lib.rs 并将程序的逻辑放入 lib.rs 中。
      * 当命令行解析逻辑比较小时，可以保留在 main.rs 中。
      * 当命令行解析开始变得复杂时，也同样将其从 main.rs 提取到 lib.rs 中。
      * 经过这些过程之后保留在 main 函数中的责任应该被限制为：
        * 使用参数值调用命令行解析逻辑
        * 设置任何其他的配置
        * 调用 lib.rs 中的 run 函数
        * 如果 run 返回错误，则处理这个错误
  * 我们将遵循测试驱动开发（Test Driven Development, TDD）的模式来逐步增加 minigrep 的搜索逻辑。这是一个软件开发技术，它遵循如下步骤：
    * 编写一个会失败的测试，并运行它以确保其因为你期望的原因失败。
    * 编写或修改刚好足够的代码来使得新的测试通过。
    * 重构刚刚增加或修改的代码，并确保测试仍然能通过。
    * 从步骤 1 开始重复！
  * 标准库提供了 eprintln! 宏来打印到标准错误流
* Rust 中的函数式语言功能：迭代器与闭包
  * Rust 的设计灵感来源于很多现存的语言和技术。其中一个显著的影响就是 函数式编程（functional programming）。函数式编程风格通常包含将函数作为参数值或其他函数的返回值、将函数赋值给变量以供之后执行等等。
  * Rust 的 闭包（closures）是可以保存进变量或作为参数传递给其他函数的匿名函数。可以在一个地方创建闭包，然后在不同的上下文中执行闭包运算。不同于函数，闭包允许捕获调用者作用域中的值。我们将展示闭包的这些功能如何复用代码和自定义行为。
  * 闭包的定义以一对竖线（|）开始，在竖线中指定闭包的参数；之所以选择这个语法是因为它与 Smalltalk 和 Ruby 的闭包定义类似。
  ```rust
    let expensive_closure = |num| {
        println!("calculating slowly...");
        thread::sleep(Duration::from_secs(2));
        num
    };

    fn  add_one_v1   (x: u32) -> u32 { x + 1 }
    let add_one_v2 = |x: u32| -> u32 { x + 1 };
    let add_one_v3 = |x|             { x + 1 };
    let add_one_v4 = |x|               x + 1  ;
  ```
  * 可以创建一个存放闭包和调用闭包结果的结构体。该结构体只会在需要结果时执行闭包，并会缓存结果值，这样余下的代码就不必再负责保存结果并可以复用该值。你可能见过这种模式被称 memoization 或 lazy evaluation。
  ```rust
    struct Cacher<T>
        where T: Fn(u32) -> u32
        {
            calculation: T,
            value: Option<u32>,
        }
  ```
  * 不过闭包还有另一个函数所没有的功能：他们可以捕获其环境并访问其被定义的作用域的变量。
  * 闭包可以通过三种方式捕获其环境，他们直接对应函数的三种获取参数的方式：获取所有权，可变借用和不可变借用。这三种捕获值的方式被编码为如下三个 Fn trait：
    * FnOnce 消费从周围作用域捕获的变量，闭包周围的作用域被称为其 环境，environment。为了消费捕获到的变量，闭包必须获取其所有权并在定义闭包时将其移动进闭包。其名称的 Once 部分代表了闭包不能多次获取相同变量的所有权的事实，所以它只能被调用一次。
    * FnMut 获取可变的借用值所以可以改变其环境
    * Fn 从其环境获取不可变的借用值
  * 如果你希望强制闭包获取其使用的环境值的所有权，可以在参数列表前使用 move 关键字。这个技巧在将闭包传递给新线程以便将数据移动到新线程中时最为实用。
  * 迭代器模式允许你对一个项的序列进行某些处理。迭代器（iterator）负责遍历序列中的每一项和决定序列何时结束的逻辑。当使用迭代器时，我们无需重新实现这些逻辑。
  * 在 Rust 中，迭代器是 惰性的（lazy），这意味着直到调用方法消费迭代器之前它都不会有效果。
  ```rust
    let v1 = vec![1, 2, 3];

    let v1_iter = v1.iter();

    for val in v1_iter {
        println!("Got: {}", val);
    }
  ```
  * 迭代器都实现了一个叫做 Iterator 的定义于标准库的 trait。
  * 这些调用 next 方法的方法被称为 消费适配器（consuming adaptors），因为调用他们会消耗迭代器。一个消费适配器的例子是 sum 方法。
  ```rust
    #[test]
    fn iterator_sum() {
        let v1 = vec![1, 2, 3];

        let v1_iter = v1.iter();

        let total: i32 = v1_iter.sum();

        assert_eq!(total, 6);
    }
  ```
  * Iterator trait 中定义了另一类方法，被称为 迭代器适配器（iterator adaptors），他们允许我们将当前迭代器变为不同类型的迭代器。可以链式调用多个迭代器适配器。不过因为所有的迭代器都是惰性的，必须调用一个消费适配器方法以便获取迭代器适配器调用的结果。
  ```rust
    let v1: Vec<i32> = vec![1, 2, 3];

    let v2: Vec<_> = v1.iter().map(|x| x + 1).collect();

    assert_eq!(v2, vec![2, 3, 4]);
  ```
  * 迭代器的 filter 方法获取一个使用迭代器的每一个项并返回布尔值的闭包。如果闭包返回 true，其值将会包含在 filter 提供的新迭代器中。如果闭包返回 false，其值不会包含在结果迭代器中。
  ```rust
    #[derive(PartialEq, Debug)]
    struct Shoe {
        size: u32,
        style: String,
    }

    fn shoes_in_my_size(shoes: Vec<Shoe>, shoe_size: u32) -> Vec<Shoe> {
        shoes.into_iter()
            .filter(|s| s.size == shoe_size)
            .collect()
    }

    #[test]
    fn filters_by_size() {
        let shoes = vec![
            Shoe { size: 10, style: String::from("sneaker") },
            Shoe { size: 13, style: String::from("sandal") },
            Shoe { size: 10, style: String::from("boot") },
        ];

        let in_my_size = shoes_in_my_size(shoes, 10);

        assert_eq!(
            in_my_size,
            vec![
                Shoe { size: 10, style: String::from("sneaker") },
                Shoe { size: 10, style: String::from("boot") },
            ]
        );
    }
  ```
  * 可以实现 Iterator trait 来创建任何我们希望的迭代器。正如之前提到的，定义中唯一要求提供的方法就是 next 方法。一旦定义了它，就可以使用所有其他由 Iterator trait 提供的拥有默认实现的方法来创建自定义迭代器了！
  * 迭代器，作为一个高级的抽象，被编译成了与手写的底层代码大体一致性能代码。迭代器是 Rust 的 零成本抽象（zero-cost abstractions）之一，它意味着抽象并不会强加运行时开销
* 进一步认识 Cargo 和 Crates.io
  * Cargo 有两个主要的配置：运行 cargo build 时采用的 dev 配置和运行 cargo build --release 的 release 配置。dev 配置被定义为开发时的好的默认配置，release 配置则有着良好的发布构建的默认配置。
  * Rust 也有特定的用于文档的注释类型，通常被称为 文档注释（documentation comments），他们会生成 HTML 文档。这些 HTML 展示公有 API 文档注释的内容，他们意在让对库感兴趣的程序员理解如何 使用 这个 crate，而不是它是如何被 实现 的。
  * 文档注释使用 /// 而不是 // 并支持 Markdown 注解来格式化文本。文档注释就位于需要文档的项的之前。
  ```rust
    /// Adds one to the number given.
    ///
    /// # Examples
    ///
    /// ```
    /// let five = 5;
    ///
    /// assert_eq!(6, my_crate::add_one(5));
    /// ```
    pub fn add_one(x: i32) -> i32 {
        x + 1
    }
  ```
  * 示例 14-1 中使用了 # Examples Markdown 标题在 HTML 中创建了一个以 “Examples” 为标题的部分。其他一些 crate 作者经常在文档注释中使用的部分有：
    * Panics：这个函数可能会 panic! 的场景。并不希望程序崩溃的函数调用者应该确保他们不会在这些情况下调用此函数。
    * Errors：如果这个函数返回 Result，此部分描述可能会出现何种错误以及什么情况会造成这些错误，这有助于调用者编写代码来采用不同的方式处理不同的错误。
    * Safety：如果这个函数使用 unsafe 代码（这会在第十九章讨论），这一部分应该会涉及到期望函数调用者支持的确保 unsafe 块中代码正常工作的不变条件（invariants）
  * 还有另一种风格的文档注释，//!，这为包含注释的项，而不是注释之后的项增加文档。这通常用于 crate 根文件（通常是 src/lib.rs）或模块的根文件为 crate 或模块整体提供文档。
  ```rust
    //! # My Crate
    //!
    //! `my_crate` is a collection of utilities to make performing certain
    //! calculations more convenient.

    /// Adds one to the number given.
    // --snip--
  ```
  * 你可以选择使用 pub use 重导出（re-export）项来使公有结构不同于私有结构。重导出获取位于一个位置的公有项并将其公开到另一个位置，好像它就定义在这个新位置一样。
  ```rust
    //! # Art
    //!
    //! A library for modeling artistic concepts.

    pub use kinds::PrimaryColor;
    pub use kinds::SecondaryColor;
    pub use utils::mix;

    pub mod kinds {
        // --snip--
    }

    pub mod utils {
        // --snip--
    }
  ```
  * 你可能会发现，随着项目开发的深入，库 crate 持续增大，而你希望将其进一步拆分成多个库 crate。对于这种情况，Cargo 提供了一个叫 工作空间（workspaces）的功能，它可以帮助我们管理多个相关的协同开发的包。
  * 工作空间 是一系列共享同样的 Cargo.lock 和输出目录的包。让我们使用工作空间创建一个项目，这里采用常见的代码这样就可以关注工作空间的结构了。
  * `cargo install`
  * Cargo 被设计为可以通过新的子命令而无须修改 Cargo 自身来进行扩展。如果 $PATH 中有类似 cargo-something 的二进制文件，就可以通过 cargo something 来像 Cargo 子命令一样运行它。像这样的自定义命令也可以运行 cargo --list 来展示出来。
* 智能指针
  * 指针 （pointer）是一个包含内存地址的变量的通用概念。这个地址引用，或 “指向”（points at）一些其他数据。Rust 中最常见的指针是第四章介绍的 引用（reference）。引用以 & 符号为标志并借用了他们所指向的值。除了引用数据它们没有任何其他特殊功能。它们也没有任何额外开销，所以应用的最多。
  * 另一方面，智能指针（smart pointers）是一类数据结构，他们的表现类似指针，但是也拥有额外的元数据和功能。智能指针的概念并不为 Rust 所独有；其起源于 C++ 并存在于其他语言中。Rust 标准库中不同的智能指针提供了多于引用的额外功能。
  * 在 Rust 中，普通引用和智能指针的一个额外的区别是引用是一类只借用数据的指针；相反大部分情况，智能指针 拥有 他们指向的数据。
  * 智能指针通常使用结构体实现。智能指针区别于常规结构体的显著特性在于其实现了 Deref 和 Drop trait。Deref trait 允许智能指针结构体实例表现的像引用一样，这样就可以编写既用于引用又用于智能指针的代码。Drop trait 允许我们自定义当智能指针离开作用域时运行的代码。
  * 最简单直接的智能指针是 box，其类型是 Box<T>。 box 允许你将一个值放在堆上而不是栈上。留在栈上的则是指向堆数据的指针。
  * 除了数据被储存在堆上而不是栈上之外，box 没有性能损失，不过也没有很多额外的功能。他们多用于如下场景：
    * 当有一个在编译时未知大小的类型，而又想要在需要确切大小的上下文中使用这个类型值的时候
    * 当有大量数据并希望在确保数据不被拷贝的情况下转移所有权的时候
    * 当希望拥有一个值并只关心它的类型是否实现了特定 trait 而不是其具体类型的时候
  ```rust
    enum List {
        Cons(i32, Box<List>),
        Nil,
    }

    use List::{Cons, Nil};

    fn main() {
        let list = Cons(1,
            Box::new(Cons(2,
                Box::new(Cons(3,
                    Box::new(Nil))))));
    }
  ```
  * 实现 Deref trait 允许我们重载 解引用运算符（dereference operator）*（与乘法运算符或 glob 运算符相区别）。通过这种方式实现 Deref trait 可以被当作常规引用来对待，可以编写操作引用的代码并用于智能指针。
  ```rust
    struct MyBox<T>(T);

    impl<T> MyBox<T> {
        fn new(x: T) -> MyBox<T> {
            MyBox(x)
        }
    }

    fn main() {
      use std::ops::Deref;

      struct MyBox<T>(T);
      impl<T> Deref for MyBox<T> {
          type Target = T;

          fn deref(&self) -> &T {
              &self.0
          }
      }
    }
  ```
  * 解引用强制多态（deref coercions）是 Rust 出于方便的考虑作用于函数或方法的参数的。其将实现了 Deref 的类型的引用转换为 Deref 所能够将原始类型转换的类型的引用。解引用强制多态发生于当作为参数传递给函数或方法的特定类型的引用不同于函数或方法签名中定义参数类型的时候，这时会有一系列的 deref 方法调用会将提供的类型转换为参数所需的类型。
  * 对于智能指针模式来说另一个重要的 trait 是 Drop。Drop 允许我们在值要离开作用域时执行一些代码。可以为任何类型提供 Drop trait 的实现，同时所指定的代码被用于释放类似于文件或网络连接的资源。
  ```rust
    struct CustomSmartPointer {
        data: String,
    }

    impl Drop for CustomSmartPointer {
        fn drop(&mut self) {
            println!("Dropping CustomSmartPointer with data `{}`!", self.data);
        }
    }

    fn main() {
        let c = CustomSmartPointer { data: String::from("my stuff") };
        let d = CustomSmartPointer { data: String::from("other stuff") };
        println!("CustomSmartPointers created.");
    }
  ```
  * 我们并不能直截了当的禁用 drop 这个功能。通常也不需要禁用 drop ；整个 Drop trait 存在的意义在于其是自动处理的。然而，有时你可能需要提早清理某个值。一个例子是当使用智能指针管理锁时；你可能希望强制运行 drop 方法来释放锁以便作用域中的其他代码可以获取锁。Rust 并不允许我们主动调用 Drop trait 的 drop 方法；当我们希望在作用域结束之前就释放变量的话，我们应该使用的是由标准库提供的 std::mem::drop。
  * 为了启用多所有权，Rust 有一个叫做 Rc<T> 的类型。其名称为 引用计数（reference counting）的缩写。引用计数意味着记录一个值引用的数量来知晓这个值是否仍在被使用。如果某个值有零个引用，就代表没有任何有效引用并可以被清理。
  ```rust
    enum List {
        Cons(i32, Rc<List>),
        Nil,
    }

    use List::{Cons, Nil};
    use std::rc::Rc;

    fn main() {
        let a = Rc::new(Cons(5, Rc::new(Cons(10, Rc::new(Nil)))));
        let b = Cons(3, Rc::clone(&a));
        let c = Cons(4, Rc::clone(&a));
    }
  ```
  * Rc<T> 允许通过不可变引用来只读的在程序的多个部分共享数据。如果 Rc<T> 也允许多个可变引用，则会违反第四章讨论的借用规则之一：相同位置的多个可变借用可能造成数据竞争和不一致。
  * 内部可变性（Interior mutability）是 Rust 中的一个设计模式，它允许你即使在有不可变引用时改变数据，这通常是借用规则所不允许的。为此，该模式在数据结构中使用 unsafe 代码来模糊 Rust 通常的可变性和借用规则。我们还未讲到不安全代码；第十九章会学习它们。当可以确保代码在运行时会遵守借用规则，即使编译器不能保证的情况，可以选择使用那些运用内部可变性模式的类型。所涉及的 unsafe 代码将被封装进安全的 API 中，而外部类型仍然是不可变的。
  * 不同于 Rc<T>，RefCell<T> 代表其数据的唯一的所有权。那么是什么让 RefCell<T> 不同于像 Box<T> 这样的类型呢？回忆一下第四章所学的借用规则：
    * 在任意给定时间，只能 拥有如下中的一个：
      * 一个可变引用。
      * 任意数量的不可变引用。
    * 引用必须总是有效的。
  * 对于引用和 Box<T>，借用规则的不可变性作用于编译时。对于 RefCell<T>，这些不可变性作用于 运行时。对于引用，如果违反这些规则，会得到一个编译错误。而对于RefCell<T>，违反这些规则会 panic!。
  * 特定情况下在值的方法内部能够修改自身是很有用的；而不是在其他代码中，此时值仍然是不可变。值方法外部的代码不能修改其值。RefCell<T> 是一个获得内部可变性的方法。RefCell<T> 并没有完全绕开借用规则，编译器中的借用检查器允许内部可变性并相应的在运行时检查借用规则。如果违反了这些规则，会得到 panic! 而不是编译错误。
  ```rust
    #[cfg(test)]
    mod tests {
        use super::*;
        use std::cell::RefCell;

        struct MockMessenger {
            sent_messages: RefCell<Vec<String>>,
        }

        impl MockMessenger {
            fn new() -> MockMessenger {
                MockMessenger { sent_messages: RefCell::new(vec![]) }
            }
        }

        impl Messenger for MockMessenger {
            fn send(&self, message: &str) {
                self.sent_messages.borrow_mut().push(String::from(message));
            }
        }

        #[test]
        fn it_sends_an_over_75_percent_warning_message() {
            // --snip--

            assert_eq!(mock_messenger.sent_messages.borrow().len(), 1);
        }
    }
  ```
  * 当创建不可变和可变引用时，我们分别使用 & 和 &mut 语法。对于 RefCell<T> 来说，则是 borrow 和 borrow_mut 方法，这属于 RefCell<T> 安全 API 的一部分。borrow 方法返回 Ref 类型的智能指针，borrow_mut 方法返回 RefMut 类型的智能指针。这两个类型都实现了 Deref 所以可以当作常规引用对待。
  * RefCell<T> 记录当前有多少个活动的 Ref 和 RefMut 智能指针。每次调用 borrow，RefCell<T> 将活动的不可变借用计数加一。当 Ref 值离开作用域时，不可变借用计数减一。就像编译时借用规则一样，RefCell<T> 在任何时候只允许有多个不可变借用或一个可变借用。
  * RefCell<T> 的一个常见用法是与 Rc<T> 结合。回忆一下 Rc<T> 允许对相同数据有多个所有者，不过只能提供数据的不可变访问。如果有一个储存了 RefCell<T> 的 Rc<T> 的话，就可以得到有多个所有者 并且 可以修改的值了！
  * Rust 的内存安全保证使其 难以 意外的制造永远也不会被清理的内存（被称为 内存泄露（memory leak）），但并不是不可能。完全的避免内存泄露并不是同在编译时拒绝数据竞争一样为 Rust 的保证之一，这意味着内存泄露在 Rust 被认为是内存安全的。这一点可以通过 Rc<T> 和 RefCell<T> 看出：有可能会创建个个项之间相互引用的引用。这会造成内存泄露，因为每一项的引用计数将永远也到不了 0，其值也永远也不会被丢弃。
  * 可以通过调用 Rc::downgrade 并传递 Rc 实例的引用来创建其值的 弱引用（weak reference）。调用 Rc::downgrade 时会得到 Weak<T> 类型的智能指针。不同于将 Rc 实例的 strong_count 加一，调用 Rc::downgrade 会将 weak_count 加一。Rc 类型使用 weak_count 来记录其存在多少个 Weak<T> 引用，类似于 strong_count。其区别在于 weak_count 无需计数为 0 就能使 Rc 实例被清理。
  * 因为 Weak<T> 引用的值可能已经被丢弃了，为了使用 Weak<T> 所指向的值，我们必须确保其值仍然有效。为此可以调用 Weak<T> 实例的 upgrade 方法，这会返回 Option<Rc<T>>。如果 Rc 值还未被丢弃则结果是 Some，如果 Rc 已经被丢弃则结果是 None。因为 upgrade 返回一个 Option，我们确信 Rust 会处理 Some 和 None的情况，并且不会有一个无效的指针。
* 无畏并发
  * 并发编程（Concurrent programming），代表程序的不同部分相互独立的执行，而 并行编程（parallel programming）代表程序不同部分于同时执行，这两个概念随着计算机越来越多的利用多处理器的优势时显得愈发重要。
  * 在大部分现代操作系统中，执行中程序的代码运行于一个 进程（process）中，操作系统则负责管理多个进程。在程序内部，也可以拥有多个同时运行的独立部分。这个运行这些独立部分的功能被称为 线程（threads）。
  * 为了创建一个新线程，需要调用 thread::spawn 函数并传递一个闭包（第十三章学习了闭包），其包含希望在新线程运行的代码。
  ```rust
    use std::thread;
    use std::time::Duration;

    fn main() {
        thread::spawn(|| {
            for i in 1..10 {
                println!("hi number {} from the spawned thread!", i);
                thread::sleep(Duration::from_millis(1));
            }
        });

        for i in 1..5 {
            println!("hi number {} from the main thread!", i);
            thread::sleep(Duration::from_millis(1));
        }
    }
  ```
  * thread::sleep 调用强制线程停止执行一小段时间，这会允许其他不同的线程运行。这些线程可能会轮流运行，不过并不保证如此：这依赖操作系统如何调度线程。
  * 可以通过将 thread::spawn 的返回值储存在变量中来修复新建线程部分没有执行或者完全没有执行的问题。thread::spawn 的返回值类型是 JoinHandle。JoinHandle 是一个拥有所有权的值，当对其调用 join 方法时，它会等待其线程结束。
  ```rust
    use std::thread;
    use std::time::Duration;

    fn main() {
        let handle = thread::spawn(|| {
            for i in 1..10 {
                println!("hi number {} from the spawned thread!", i);
                thread::sleep(Duration::from_millis(1));
            }
        });

        for i in 1..5 {
            println!("hi number {} from the main thread!", i);
            thread::sleep(Duration::from_millis(1));
        }

        handle.join().unwrap();
    }
  ```
  * move 关键字覆盖了 Rust 默认保守的借用：其也不允许我们违反所有权规则。
  * 一个人气正在上升的确保安全并发的方式是 消息传递（message passing），这里线程或 actor 通过发送包含数据的消息来相互沟通。这个思想来源于 Go 编程语言文档中的口号：不要共享内存来通讯；而是要通讯来共享内存。
  * Rust 中一个实现消息传递并发的主要工具是 通道（channel），一个 Rust 标准库提供了其实现的编程概念。
  * 编程中的通道有两部分组成，一个发送者（transmitter）和一个接收者（receiver）。
  * 使用 mpsc::channel 函数创建一个新的通道；mpsc 是 多个生产者，单个消费者（multiple producer, single consumer）的缩写。简而言之，Rust 标准库实现通道的方式意味着一个通道可以有多个产生值的 发送（sending）端，但只能有一个消费这些值的 接收（receiving）端。
  ```rust
    use std::sync::mpsc;

    fn main() {
        let (tx, rx) = mpsc::channel();
    }
  ```
  * 通道的接收端有两个有用的方法：recv 和 try_recv。
  * 所有权规则在消息传递中扮演了重要角色，其有助于我们编写安全的并发代码。在并发编程中避免错误是在整个 Rust 程序中必须思考所有权所换来的一大优势。
  * 通过克隆发送者来创建多个生产者
  * 互斥器（mutex）是 “mutual exclusion” 的缩写，也就是说，任意时刻，其只允许一个线程访问某些数据。为了访问互斥器中的数据，线程首先需要通过获取互斥器的 锁（lock）来表明其希望访问数据。锁是一个作为互斥器一部分的数据结构，它记录谁有数据的排他访问权。因此，我们描述互斥器为通过锁系统 保护（guarding）其数据。
  ```rust
    use std::sync::Mutex;

    fn main() {
        // 我们使用关联函数 new 来创建一个 Mutex<T>。使用 lock 方法获取锁，以访问互斥器中的数据。这个调用会阻塞当前线程，直到我们拥有锁为止
        let m = Mutex::new(5);

        {
            let mut num = m.lock().unwrap();
            *num = 6;
        }

        println!("m = {:?}", m);
    }
  ```
  * Mutex<T> 是一个智能指针。更准确的说，lock 调用 返回 一个叫做 MutexGuard 的智能指针。这个智能指针实现了 Deref 来指向其内部数据；其也提供了一个 Drop 实现当 MutexGuard 离开作用域时自动释放锁，这正发生于示例 16-12 内部作用域的结尾。为此，我们不会冒忘记释放锁并阻塞互斥器为其它线程所用的风险，因为锁的释放是自动发生的。
  * Arc<T> 正是 这么一个类似 Rc<T> 并可以安全的用于并发环境的类型。字母 “a” 代表 原子性（atomic），所以这是一个原子引用计数（atomically reference counted）类型。原子性是另一类这里还未涉及到的并发原语：请查看标准库中 std::sync::atomic 的文档来获取更多细节。其中的要点就是：原子性类型工作起来类似原始类型，不过可以安全的在线程间共享。
  ```rust
    use std::sync::{Mutex, Arc};
    use std::thread;

    fn main() {
        let counter = Arc::new(Mutex::new(0));
        let mut handles = vec![];

        for _ in 0..10 {
            let counter = Arc::clone(&counter);
            let handle = thread::spawn(move || {
                let mut num = counter.lock().unwrap();

                *num += 1;
            });
            handles.push(handle);
        }

        for handle in handles {
            handle.join().unwrap();
        }

        println!("Result: {}", *counter.lock().unwrap());
    }
  ```
  * Send 标记 trait 表明类型的所有权可以在线程间传递。几乎所有的 Rust 类型都是Send 的，不过有一些例外，包括 Rc<T>：这是不能 Send 的，因为如果克隆了 Rc<T> 的值并尝试将克隆的所有权转移到另一个线程，这两个线程都可能同时更新引用计数。为此，Rc<T> 被实现为用于单线程场景，这时不需要为拥有线程安全的引用计数而付出性能代价。
  * Sync 标记 trait 表明一个实现了 Sync 的类型可以安全的在多个线程中拥有其值的引用。换一种方式来说，对于任意类型 T，如果 &T（T 的引用）是 Send 的话 T 就是 Sync 的，这意味着其引用就可以安全的发送到另一个线程。类似于 Send 的情况，基本类型是 Sync 的，完全由 Sync 的类型组成的类型也是 Sync 的。
* Rust 的面向对象特性
  * 当对泛型使用 trait bound 时编译器所进行单态化处理：编译器为每一个被泛型类型参数代替的具体类型生成了非泛型的函数和方法实现。单态化所产生的代码进行 静态分发（static dispatch）。静态分发发生于编译器在编译时就知晓调用了什么方法的时候。这与 动态分发 （dynamic dispatch）相对，这时编译器在编译时无法知晓调用了什么方法。在这种情况下，编译器会生成在运行时确定调用了什么方法的代码。
* 模式用来匹配值的结构
  * 一个模式常用的位置是 match 表达式的分支。在形式上 match 表达式由 match 关键字、用于匹配的值和一个或多个分支构成，这些分支包含一个模式和在值匹配分支的模式时运行的表达式：
  ```rust
    match VALUE {
        PATTERN => EXPRESSION,
        PATTERN => EXPRESSION,
        PATTERN => EXPRESSION,
    }
  ```
  * match 表达式必须是 穷尽（exhaustive）的，意为 match 表达式所有可能的值都必须被考虑到。一个确保覆盖每个可能值的方法是在最后一个分支使用捕获所有的模式 —— 比如，一个匹配任何值的名称永远也不会失败，因此可以覆盖所有匹配剩下的情况。
  * 有一个特定的模式 _ 可以匹配所有情况，不过它从不绑定任何变量。
  * if let 表达式，以及它是如何主要用于编写等同于只关心一个情况的 match 语句简写的。if let 可以对应一个可选的带有代码的 else 在 if let 中的模式不匹配时运行。
  ```rust
    fn main() {
        let favorite_color: Option<&str> = None;
        let is_tuesday = false;
        let age: Result<u8, _> = "34".parse();

        if let Some(color) = favorite_color {
            println!("Using your favorite color, {}, as the background", color);
        } else if is_tuesday {
            println!("Tuesday is green day!");
        } else if let Ok(age) = age {
            if age > 30 {
                println!("Using purple as the background color");
            } else {
                println!("Using orange as the background color");
            }
        } else {
            println!("Using blue as the background color");
        }
    }
  ```
  * 一个与 if let 结构类似的是 while let 条件循环，它允许只要模式匹配就一直进行 while 循环。
  ```rust
    let mut stack = Vec::new();

    stack.push(1);
    stack.push(2);
    stack.push(3);

    while let Some(top) = stack.pop() {
        println!("{}", top);
    }
  ```
  * for 循环是 Rust 中最常见的循环结构，不过还没有讲到的是 for 可以获取一个模式。在 for 循环中，模式是 for 关键字直接跟随的值，正如 for x in y 中的 x。
  ```rust
    let v = vec!['a', 'b', 'c'];

    for (index, value) in v.iter().enumerate() {
        println!("{} is at index {}", value, index);
    }
  ```
  * let 语句更为正式的样子如下：`let PATTERN = EXPRESSION;`
  * 函数参数也可以是模式。
  ```rust
    fn print_coordinates(&(x, y): &(i32, i32)) {
        println!("Current location: ({}, {})", x, y);
    }

    fn main() {
        let point = (3, 5);
        print_coordinates(&point);
    }
  ```
  * 模式有两种形式：refutable（可反驳的）和 irrefutable（不可反驳的）。能匹配任何传递的可能值的模式被称为是 不可反驳的（irrefutable）。一个例子就是 let x = 5; 语句中的 x，因为 x 可以匹配任何值所以不可能会失败。对某些可能的值进行匹配会失败的模式被称为是 可反驳的（refutable）。一个这样的例子便是 if let Some(x) = a_value 表达式中的 Some(x)；如果变量 a_value 中的值是 None 而不是 Some，那么 Some(x) 模式不能匹配。
  * 所有的模式语法
    * 匹配字面值
    * 匹配命名变量
    * 多个模式
      * 在 match 表达式中，可以使用 | 语法匹配多个模式，它代表 或（or）的意思。
    * 通过 ... 匹配值的范围
    * 解构并分解值
    * 忽略模式中的值
      * 使用 _ 忽略整个值
      * 使用嵌套的 _ 忽略部分值
      * 通过在名字前以一个下划线开头来忽略未使用的变量
      * 用 .. 忽略剩余值
    * 使用 ref 和 ref mut 在模式中创建引用
    ```rust
      let robot_name = Some(String::from("Bors"));

      match robot_name {
          Some(ref name) => println!("Found a name: {}", name),
          None => (),
      }

      println!("robot_name is: {:?}", robot_name);
    ```
    * 匹配守卫提供的额外条件
    ```rust
      let num = Some(4);

      match num {
          Some(x) if x < 5 => println!("less than five: {}", x),
          Some(x) => println!("{}", x),
          None => (),
      }
    ```
    * @ 绑定
      * at 运算符 @ 允许我们在创建一个存放值的变量的同时测试其值是否匹配模式。
* 高级特征
  * 不安全 Rust：用于当需要舍弃 Rust 的某些保证并由你自己负责维持这些保证
    * 不安全 Rust 之所以存在，是因为静态分析本质上是保守的。当编译器尝试确定一段代码是否支持某个保证时，拒绝一些有效的程序比接受无效程序要好一些。这必然意味着有时代码可能是合法的，但是 Rust 不这么认为！在这种情况下，可以使用不安全代码告诉编译器，“相信我，我知道我在干什么。”这么做的缺点就是你只能靠自己了：如果不安全代码出错了，比如解引用空指针，可能会导致不安全的内存使用。
    * unsafe 并不会关闭借用检查器或禁用任何其他 Rust 安全检查：如果在不安全代码中使用引用，其仍会被检查。
    * 解引用裸指针
      * 不安全 Rust 有两个被称为 裸指针（raw pointers）的类似于引用的新类型。和引用一样，裸指针是可变或不可变的，分别写作 *const T 和 *mut T。这里的星号不是解引用运算符；它是类型名称的一部分。在裸指针的上下文中，“裸指针” 意味着指针解引用之后不能直接赋值。
      ```rust
        let mut num = 5;

        let r1 = &num as *const i32;
        let r2 = &mut num as *mut i32;
      ```
      * 可以在安全代码中 创建 裸指针，只是不能在不安全块之外 解引用 裸指针，稍后便会看到。
      * 我们要做的就是对裸指针使用解引用运算符 *，只要求一个 unsafe 块
      ```rust
        let mut num = 5;

        let r1 = &num as *const i32;
        let r2 = &mut num as *mut i32;

        unsafe {
            println!("r1 is: {}", *r1);
            println!("r2 is: {}", *r2);
        }
      ```
      * 有时你的 Rust 代码可能需要与其他语言编写的代码交互。为此 Rust 有一个关键字，extern，有助于创建和使用 外部函数接口（Foreign Function Interface， FFI）。
      * 全局变量在 Rust 中被称为 静态（static）变量。
      ```rust
        static HELLO_WORLD: &str = "Hello, world!";

        fn main() {
            println!("name is: {}", HELLO_WORLD);
        }
      ```
  * 高级生命周期：用于复杂生命周期情况的语法
    * 生命周期子类型（lifetime subtyping），一个确保某个生命周期长于另一个生命周期的方式
      * Rust 的 生命周期子类型（lifetime subtyping）功能，这是一个指定一个生命周期不会短于另一个的方法。在声明生命周期参数的尖括号中，可以照常声明一个生命周期 'a，并通过语法 'b: 'a 声明一个不短于 'a 的生命周期 'b。
      ```rust
        struct Parser<'c, 's: 'c> {
            context: &'c Context<'s>,
        }
      ```
    * 生命周期 bound（lifetime bounds），用于指定泛型引用的生命周期
      ```rust
        struct Ref<'a, T: 'a>(&'a T);

        struct StaticRef<T: 'static>(&'static T);
      ```
    * trait 对象生命周期（trait object lifetimes），以及他们是如何推断的，以及何时需要指定
  * 高级 trait：与 trait 相关的关联类型，默认类型参数，完全限定语法（fully qualified syntax），超（父）trait（supertraits）和 newtype 模式
    * 关联类型（associated types）是一个将类型占位符与 trait 相关联的方式，这样 trait 的方法签名中就可以使用这些占位符类型。trait 的实现者会针对特定的实现在这个类型的位置指定相应的具体类型。如此可以定义一个使用多种类型的 trait，直到实现此 trait 时都无需知道这些类型具体是什么。
    * 比较陌生的部分是尖括号中的 RHS=Self：这个语法叫做 默认类型参数（default type parameters）。RHS 是一个泛型类型参数 —— “right hand side” 的缩写 —— 它用于 add 方法中的 rhs 参数。如果实现 Add trait 时不指定 RHS 的具体类型，RHS 的类型将是默认的 Self 类型，也就是在其上实现 Add 的类型。
    ```rust
      trait Add<RHS=Self> {
          type Output;

          fn add(self, rhs: RHS) -> Self::Output;
      }
    ```
    * 完全限定语法
    ```rust
      fn main() {
          println!("A baby dog is called a {}", <Dog as Animal>::baby_name());
      }
    ```
    * 有时我们可能会需要某个 trait 使用另一个 trait 的功能。在这种情况下，需要能够依赖相关的 trait 也被实现。这个所需的 trait 是我们实现的 trait 的 父（超） trait（supertrait）。
    ```rust
      use std::fmt;

      trait OutlinePrint: fmt::Display {
          fn outline_print(&self) {
              let output = self.to_string();
              let len = output.len();
              println!("{}", "*".repeat(len + 4));
              println!("*{}*", " ".repeat(len + 2));
              println!("* {} *", output);
              println!("*{}*", " ".repeat(len + 2));
              println!("{}", "*".repeat(len + 4));
          }
      }
    ```
    * newtype 模式（newtype pattern），它涉及到在一个元组结构体（第五章 “用没有命名字段的元组结构体来创建不同的类型” 部分介绍了元组结构体）中创建一个新类型。这个元组结构体带有一个字段作为希望实现 trait 的类型的简单封装。接着这个封装类型对于 crate 是本地的，这样就可以在这个封装上实现 trait。“Newtype” 是一个源自（U.C.0079，逃）Haskell 编程语言的概念。使用这个模式没有运行时性能惩罚，这个封装类型在编译时就被省略了。
    ```rust
      use std::fmt;

      struct Wrapper(Vec<String>);

      impl fmt::Display for Wrapper {
          fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
              write!(f, "[{}]", self.0.join(", "))
          }
      }

      fn main() {
          let w = Wrapper(vec![String::from("hello"), String::from("world")]);
          println!("w = {}", w);
      }
    ```
  * 高级类型：关于 newtype 模式的更多内容，类型别名，“never” 类型和动态大小类型
    * 连同 newtype 模式，Rust 还提供了声明 类型别名（type alias）的能力，使用 type 关键字来给予现有类型另一个名字。
    ```rust
      type Kilometers = i32;
    ```
    * Rust 有一个叫做 ! 的特殊类型。在类型理论术语中，它被称为 empty type，因为它没有值。我们更倾向于称之为 never type。这个名字描述了它的作用：在函数从不返回的时候充当返回值。
    * 因为 Rust 需要知道例如应该为特定类型的值分配多少空间这样的信息其类型系统的一个特定的角落可能令人迷惑：这就是 动态大小类型（dynamically sized types）的概念。这有时被称为 “DST” 或 “unsized types”，这些类型允许我们处理只有在运行时才知道大小的类型。
    * 为了处理 DST，Rust 有一个特定的 trait 来决定一个类型的大小是否在编译时可知：这就是 Sized trait。这个 trait 自动为编译器在编译时就知道大小的类型实现。另外，Rust 隐式的为每一个泛型函数增加了 Sized bound。
  * 高级函数和闭包：函数指针和返回闭包
    * 通过函数指针允许我们使用函数作为另一个函数的参数。函数的类型是 fn （使用小写的 “f” ）以免与 Fn 闭包 trait 相混淆。fn 被称为函数指针（function pointer）。指定参数为函数指针的语法类似于闭包
    * fn 是一个类型而不是一个 trait，所以直接指定 fn 作为参数而不是声明一个带有 Fn 作为 trait bound 的泛型参数。
    * 闭包表现为 trait，这意味着不能直接返回闭包。对于大部分需要返回 trait 的情况，可以使用实现了期望返回的 trait 的具体类型来替代函数的返回值。但是这不能用于闭包，因为他们没有一个可返回的具体类型；例如不允许使用函数指针 fn 作为返回值类型。
