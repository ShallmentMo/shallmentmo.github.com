---
layout: post
title:  "《Ruby 原理剖析》笔记"
date:   2018-07-11 23:27:30 +0800
categories: posts
---

之前就看过部分原版的《Ruby Under a Microscope》，但是因为英文水平并未能完成，现在出了中文版，所以决定重拾起来看完。

* 第一章 分词与语法解析

  * Ruby 处理代码的工序：

    Ruby 代码 =》 分词 =》解析 =》编译 =》 YARV 指令

  * Ripper

    `Ripper.lex("p nil")`

    `Ripper.sexp("p nil")`

  * Ruby 构建过程会提前运行 Bison：

    语法规则(parse.y) =》 生成解析器（Bison）=》解析代码(parse.c 提供分词和解析的功能)

* 第二章 编译

  * Ruby 1.8 及早期版本没有包含编译器，而是在分词和语法解析完成后立即执行代码。

  * 如果使用 YARV（类似于JVM），代码首先会被编译成字节码（bytecode），这是一系列能被虚拟机理解的底层指令。

  * YARV 面向堆栈的特性最吸引人的一点就是让 Ruby 编译器编译 AST 节点更加容易。

  * 显示 YARV 指令

    ```ruby
    RubyVM::InstructionSequence.compile("puts 2 + 2").disasm
    ```

  * 一旦 Ruby 编译器开始运行，块参数信息就会被从 AST 中复制到另一个称为本地表的数据结构中，此本地表被保存于最新生成的 YARV 指令附件。每个 YARV 指令片段和 Ruby 程序中的每个作用域都有自己的本地表。

  * Ruby 也会在这个表里保存本地变量的信息，因此这个表被称为本地表。

* 第三章 Ruby 如何执行代码

  * YARV 是面向堆栈的虚拟机。

  * YARV 内部栈和 Ruby 调用栈

  * 访问 Ruby 变量的两种方式：本地访问和动态访问。

* 第四章 控制结构与方法调度

  * 用 jump 加上 branchif 和 branchunless 指令，YARV就能实现大部分简单的控制结构。

  * 捕获表

  * Ruby 方法的 11 种类型：ISEQ、CFUNC、ATTRSET、IVAR、BMETHOD、ZSUPER、UNDEF、NOTIMPLEMENTED、OPTIMIZED、MISSING、REFINED

* 第五章 对象与类

  * 每个值都是对象，所有 Ruby 程序都由一组对象和它们彼此间发送的消息组成。通常，我们通过查看如何使用对象以及它们可以做什么来了解面向对象编程：它们如何把数据以及与这些数据相关的行为组织在一起；每个类有什么作用和目的；不同的类之间如何封装和继承。

  * Ruby 把每个自定义对象都保存在名为 RObject 的 C 结构体中。

  ```
  RObject 结构体 {
    RBasic 结构体 {
      flags; 存储各种内部专用的值
      klass; 类指针
    }
    numiv; 实例变量的数目
    ivptr; 实例变量值数组的指针
  }
  ```

  * 每个 Ruby 对象都是类指针和实例变量数组的组合。

  * 在内部，Ruby 使用了和 RObject 不一样的结构体来保存基本数据类型的值。例如，Ruby 使用 RString 结构体来保存字符串的值，使用 RArray 结构体保存数组，使用 RRegexp 结构体保存正则表达式，诸如此类。

  * 为了优化性能，Ruby 保存小值整数、符号和其他一些简单立即值时没有使用任何结构体，只是把它们放到 VALUE 内。这类 VALUE 不是指针，它们就是立即值本身。

  * 当在基本类型对象中保存实例变量时，Ruby 会把她保存在名为 generic_iv_tbl 的特殊散列里。该散列维护着基本类型对象和另外一些散列的指针的映射，而那些散列中包含了基本类型对象的所有实例变量。

  * Ruby 类就是一组方法定义和属性名字、超类指针和常量表的 Ruby 对象。

  * 类变量和类实例变量的区别：创建类变量时，Ruby 会在该类中创建唯一的值，并在其任意子类中共享该值。如果是类实例变量，那么 Ruby 会在该类及其子类中创建各自独立使用的值。

  * Ruby 使用两个独立的结构体来表示类：RClass 和 rb_classext_struct。

  ```
  RClass 结构体 {
    RBasic 结构体 {
      flags;
      klass; 类指针
    }
    m_tbl; 方法表
    iv_index_tbl; 实例级属性名字
    ptr; 指向 rb_classext_struct 的指针
  }
  rb_classext_struct {
    super; 超类指针
    iv_tbl; 类级实例变量
    const_tbl; 常量表
    origin; 用于 Module#prepend
    refined_class; 用于实现 refinement
    allocator; 用来为新对象分配内存
  }
  ```

  * 当创建新类的时候，Ruby 内部创建两个类！第一个是新创建的类，Ruby 创建新 RClass 结构体来表示类，如上面所说。但是在 Ruby 内部也创建了第二个、隐藏的类叫元类。为什么呢？是为了保存任何可能之后会为新类创建的类方法。事实上，Ruby 把元类设置成新类的类：设置新类的 RClass 结构体的 klass 指针指向元类。

* 第六章 方法查找和常量查找

  * 事实上，Ruby 内部用类来实现模块。创建模块时，Ruby 同时创建了一对 RClass/rb_classext_struct 组合，就像创建新类那样。

  * Ruby 模块是包含方法定义、超类指针和常量表的 Ruby 对象。

  ```
  RClass: module 结构体 {
    RBasic 结构体 {
      flags;
      klass; 类指针
    }
    m_tbl; 方法表
    ptr; 指向 rb_classext_struct 的指针
  }
  rb_classext_struct {
    super; 超类指针
    iv_tbl; 类级实例变量
    const_tbl; 常量表
    origin; 用于 Module#prepend
  }
  ```

  * 当包含（include）模块的时候，Ruby 把该模块的副本插入到类的超类链表中。

  * Ruby 是以完全相同的方式来实现 extend 的，只是“被 include 的类”变成了目标类的元类的超类。因此，extend 允许你为一个类增加类方法。

  * Ruby 的方法查找算法：把当前类设置为接收者，在当前类中遍历方法表，如果找到，则调用方法；如果没找到，则把当前类设置为当前类的超类。

  * 方法查找耗费的时间依赖于超类链中超类的数量。为了减少开销，Ruby 缓存了查找结果以便后续使用。它使用两种缓存来记录哪个类（或模块）实现了代码中调用的方法：全局方法缓存（global method cache）和内联方法缓存（inline method cache）。

  * Ruby 检索代码中引用的常量定义的一种方式是使用超类链，如同它进行方法查找那样。

  * Ruby 使用词法作用域来查找常量。

  * Ruby 在查找常量的时候迭代词法作用域的方式：设置 cref 为当前作用域，遍历 cref 类的常量表，如果未找到，设置 cref 为父词法作用域。

  * 检索词法作用域链 =》为每个作用域的类检查 autoload =〉检索超类链 =》为每个超类检查 autoload =〉调用 const_missing

* 第七章 散列表：Ruby 内部的主力军

* 第八章 Ruby 如何借鉴 Lisp 几十年前的理念

  * 块（Block）是 Ruby 的闭包实现。

  * Ruby 在内部使用名为 rb_block_t 的结构体来表示块。

  * lambda（或 proc）关键字会把块转换成数据值。

  * Ruby 使用了名为 RTypedData 的结构体，跟 rb_proc_t 一起来表示 proc 对象的实例。

  ```
  RTypedData 结构体 {
    RBasic 结构体 {
      flags;
      klass; 指向 Proc Class
    }
    data; 指向 rb_proc_t 结构体
  }
  rb_proc_t 结构体 {
    rb_block_t 结构体 {
      iseq;
      EP;
    }
    envval;
    is_lambda;
  }
  ```

* 第九章 元编程

  * Ruby 的元编程特性可允许程序查询关于自己的信息（有关方法、实例变量和超类的信息）。元编程特性还可以执行普通任务，比如以更灵活的方式定义方法和常量。最后，像 eval 这样的方法可允许程序生成全新的代码，在运行时解析和编译。

* 第十章 JRuby：基于 JVM 的 Ruby

  * 就像 MRI 使用了 Bison，JRuby 使用的解析器生成器叫做 Jay，它在 JRuby 构建期间创建能解析 Ruby 代码的代码。

* 第十一章 Rubinius：用 Ruby 实现的 Ruby

* 第十二章 MRI、JRuby、Rubinius 垃圾回收
