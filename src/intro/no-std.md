# A `no_std` Rust Environment 一个`no_std`（禁用标准库）的Rust环境

> 原版Github: [https://github.com/rust-embedded/book/](https://github.com/rust-embedded/book/)
>
> 本翻译依照版本：`9858872bd1b7dbba5ec27dc30d34eba00acd7ef9`
>
> 翻译人员：Flymachine
>
> 原版及翻译版均遵循以下协议：
>
>> - [MIT License Hosted]
>> - [Apache License v2.0 Hosted]
>> - [CC-BY-SA v4.0 Hosted]

[MIT License]: ./LICENSE-MIT
[Apache License v2.0]: ./LICENSE-APACHE
[CC-BY-SA v4.0]: ./LICENSE-CC-BY-SA
[MIT License Hosted]: https://opensource.org/licenses/MIT
[Apache License v2.0 Hosted]: http://www.apache.org/licenses/LICENSE-2.0
[CC-BY-SA v4.0 Hosted]: https://creativecommons.org/licenses/by-sa/4.0/legalcode

术语“嵌入式编程”（Embedded Programming）被用来指代一个广泛范围内的各种不同类型的编程。
从为只有几KB的RAM和ROM的8位MCU(比如[ST72325xx](https://www.st.com/resource/en/datasheet/st72325j6.pdf))编程，
到为能跑系统的比如有一块32/64位4核Cortex-A53 @ 1.4 GHz和1GB RAM的树莓派Raspberry Pi
([Model B 3+](https://en.wikipedia.org/wiki/Raspberry_Pi#Specifications))编程。
编写代码时将适用不同的约束/局限，具体取决于你已有的目标和用例类型。

这里有两大通用嵌入式编程类别：

## Hosted Environments 托管环境

这种类型的环境非常接近与普通PC环境。
这意味着你将获得一个系统接口（System Interface）[E.G. POSIX](https://en.wikipedia.org/wiki/POSIX)
，通过它为你提供与各种系统交互的原语，例如文件系统，网络，内存管理，线程等。
反过来，标准库通常依赖于这些原语来实现它们的功能。
你可能也有某种sysroot并受RAM/ROM使用范围的限制，也许还有一些特殊的HW或I/O口。总的来说，感觉就像在一个专用PC环境中编码一样。

## Bare Metal Environments 裸机环境
在裸机环境下，没有代码会在你的程序前加载。
没有OS提供的软件我们就无法加载标准库。
代替的是，程序以及程序使用的crates（译者注：`crate`为Rust术语，指库），只能使用硬件（裸机）去运行。
防止Rust加载标准库应使用`no_std`。
标准库的平台无关部分可通过[libcore 核心库](https://doc.rust-lang.org/core/)获得。
libcore还排除了嵌入式环境中并不总是合意的东西。
其中一个就是用于动态内存分配的内存分配器。
如果你需要这个东西或其他什么功能，经常会有一些crates提供这些功能。

### The libstd Runtime 标准库运行时
如前所述[libstd 标准库](https://doc.rust-lang.org/std/)需要某种系统集成，但这不仅仅是因为[libstd 标准库](https://doc.rust-lang.org/std/)只是提供了一种访问OS抽象概念的常用方法，它还提供了一个运行时（runtime）。
除了一些其他功能外，此运行时还负责设置堆栈溢出保护，处理命令行参数，以及在调用程序的main函数之前生成主线程。此运行时也不会在`no_std`环境中可用。

## Summary 总结
`#![no_std]`是一个crate-level（译者注：库层次）属性，表示crate将链接到core-crate（译者注：核心库）而不是std-crate（译者注：标准库）。
反过来，[libcore 核心库](https://doc.rust-lang.org/core/) crate是std crate中与平台无关的子集，它不会对程序运行的系统做出任何假设。
因此，它为诸如浮点数，字符串和切片（slices）之类的语言原语提供了API，并提供诸如原子操作和SIMD指令之类的处理器功能的API。但是它缺乏任何涉及到平台集成功能的API。
由于这些属性，no\_std和[libcore 核心库](https://doc.rust-lang.org/core/)代码可用于任何类型的引导（阶段0）代码，如引导加载程序（bootloaders），固件或内核。

### Overview 概览

| 特性                                                   | no\_std | std |
|-----------------------------------------------------------|--------|-----|
| 堆（动态内存）                                          |   *    |  ✓  |
| 集合 (Vec, HashMap等)                                  |  **    |  ✓  |
| 堆栈溢出保护                                            |   ✘    |  ✓  |
| 在main之前运行init代码                                  |   ✘    |  ✓  |
| libstd可用                                             |   ✘    |  ✓  |
| libcore可用                                            |   ✓    |  ✓  |
| 写固件，内核或引导加载程序代码                           |   ✓    |  ✘  |

\* 只有当你使用`alloc` crate并使用像[alloc-cortex-m]这样的合适的分配器时。

\** 只有当你使用`collections` crate并配置一个全局默认分配器时。

[alloc-cortex-m]: https://github.com/rust-embedded/alloc-cortex-m

## See Also 参考

* [RFC-1184](https://github.com/rust-lang/rfcs/blob/master/text/1184-stabilize-no_std.md)

>## 中文版目录
>
>1. [Introduction 导言]
>    - 1.1. [Hardware 硬件]
>    - 1.2. [no_std 禁用标准库]
>    - 1.3. [Tooling 工具]
>    - 1.4. [Installation 安装]
>        - 1.4.1. [Linux]
>        - 1.4.2. [MacOS]
>        - 1.4.3. [Windows]
>        - 1.4.4. [Verify Installation 验证安装]
>2. Getting started 新手入门
>    - 2.1. QEMU Qemu仿真软件
>    - 2.2. Hardware 硬件
>    - 2.3. Memory-mapped Registers 内存映射寄存器
>    - 2.4. Semihosting 半主机
>    - 2.5. Panicking 恐慌
>    - 2.6. Exceptions 异常
>    - 2.7. Interrupts 中断
>    - 2.8. IO 输入/输出
>3. Peripherals 外围设备
>    - 3.1. A first attempt in Rust 用Rust的第一个尝试
>    - 3.2. The Borrow Checker Borrow检查器
>    - 3.3. Singletons
>4. Static Guarantees 静态保证
>    - 4.1. Typestate Programming 类型状态编程
>    - 4.2. Peripherals as State Machines 作为状态机的外围设备
>    - 4.3. Design Contracts 设计契约
>    - 4.4. Zero Cost Abstractions 0成本抽象
>5. Portability 轻量
>6. Concurrency 并发
>7. Collections 集合
>8. Tips for embedded C developers 为嵌入式C程序员准备的提示
>9. Interoperability 互操作
>    - 9.1. A little C with your Rust 在你的Rust项目中使用一点C
>    - 9.2. A little Rust with your C 在你的C项目中使用一点Rust
>10. Unsorted topics 未分类主题
>     - 10.1. Optimizations: The speed size tradeoff 优化：速度和体积的权衡
>

[Introduction 导言]: https://rustforce.net/article?id=8da8d5bf-f2af-4c9a-b3f3-567c19488871
[Hardware 硬件]: https://rustforce.net/article?id=e86bd376-948b-4e9e-a359-c419a7206ca1
[no_std 禁用标准库]: https://rustforce.net/article?id=5697689c-b8f1-4144-88fa-6f9f45ed7f83
[Tooling 工具]: https://rustforce.net/article?id=07057df5-c646-46ee-bbf6-5ce3c7a131b7
[Installation 安装]: https://rustforce.net/article?id=93e6942e-1c7c-477e-b7da-3ff1e18e4402
[Linux]: https://rustforce.net/article?id=2342532b-c85b-41b3-ad38-62865825bb9b
[MacOS]: https://rustforce.net/article?id=0e8628b4-8d92-40eb-89cd-71ae5cf373ec
[Windows]: https://rustforce.net/article?id=ac93073d-cff7-4c96-bbaf-047eceb4f13d
[Verify Installation 验证安装]: https://rustforce.net/article?id=35d99a70-bfd0-4169-b664-de44129df484