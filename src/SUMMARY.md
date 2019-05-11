# Summary 概要

> 原版Github: [https://github.com/rust-embedded/book/](https://github.com/rust-embedded/book/)
>
> 本翻译依照版本：`9858872bd1b7dbba5ec27dc30d34eba00acd7ef9`
>
> 翻译人员：Flymachine
>
> 翻译版错误与建议反馈：flymachine@foxmail.com
>
> 原版及翻译版均遵循以下协议：
>
>> - [MIT License Hosted]
>> - [Apache License v2.0 Hosted]
>> - [CC-BY-SA v4.0 Hosted]


[MIT License]: ./../LICENSE-MIT
[Apache License v2.0]: ./../LICENSE-APACHE
[CC-BY-SA v4.0]: ./../LICENSE-CC-BY-SA
[MIT License Hosted]: https://opensource.org/licenses/MIT
[Apache License v2.0 Hosted]: http://www.apache.org/licenses/LICENSE-2.0
[CC-BY-SA v4.0 Hosted]: https://creativecommons.org/licenses/by-sa/4.0/legalcode
<!--

Definition of the organization of this book is still a work in process.

Refer to https://github.com/rust-embedded/book/issues for
more information and coordination

-->

- [Introduction](./intro/index.md)
    - [Hardware](./intro/hardware.md)
    - [`no_std`](./intro/no-std.md)
    - [Tooling](./intro/tooling.md)
    - [Installation](./intro/install.md)
        - [Linux](./intro/install/linux.md)
        - [MacOS](./intro/install/macos.md)
        - [Windows](./intro/install/windows.md)
        - [Verify Installation](./intro/install/verify.md)
- [Getting started](./start/index.md)
  - [QEMU](./start/qemu.md)
  - [Hardware](./start/hardware.md)
  - [Memory-mapped Registers](./start/registers.md)
  - [Semihosting](./start/semihosting.md)
  - [Panicking](./start/panicking.md)
  - [Exceptions](./start/exceptions.md)
  - [Interrupts](./start/interrupts.md)
  - [IO](./start/io.md)
- [Peripherals](./peripherals/index.md)
    - [A first attempt in Rust](./peripherals/a-first-attempt.md)
    - [The Borrow Checker](./peripherals/borrowck.md)
    - [Singletons](./peripherals/singletons.md)
- [Static Guarantees](./static-guarantees/index.md)
    - [Typestate Programming](./static-guarantees/typestate-programming.md)
    - [Peripherals as State Machines](./static-guarantees/state-machines.md)
    - [Design Contracts](./static-guarantees/design-contracts.md)
    - [Zero Cost Abstractions](./static-guarantees/zero-cost-abstractions.md)
- [Portability](./portability/index.md)
- [Concurrency](./concurrency/index.md)
- [Collections](./collections/index.md)
- [Tips for embedded C developers](./c-tips/index.md)
    <!-- TODO: Define Sections -->
- [Interoperability](./interoperability/index.md)
    - [A little C with your Rust](./interoperability/c-with-rust.md)
    - [A little Rust with your C](./interoperability/rust-with-c.md)
- [Unsorted topics](./unsorted/index.md)
  - [Optimizations: The speed size tradeoff](./unsorted/speed-vs-size.md)

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
>2. [Getting started 新手入门]
>    - 2.1. [QEMU Qemu仿真软件]
>    - 2.2. [Hardware 硬件_]
>    - 2.3. Memory-mapped Registers 内存映射寄存器（译者注：正在翻译中）
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
>
> 下一节 1. [Introduction 导言]

[Introduction 导言]: https://rustforce.net/article?id=8da8d5bf-f2af-4c9a-b3f3-567c19488871
[Hardware 硬件]: https://rustforce.net/article?id=e86bd376-948b-4e9e-a359-c419a7206ca1
[no_std 禁用标准库]: https://rustforce.net/article?id=5697689c-b8f1-4144-88fa-6f9f45ed7f83
[Tooling 工具]: https://rustforce.net/article?id=07057df5-c646-46ee-bbf6-5ce3c7a131b7
[Installation 安装]: https://rustforce.net/article?id=93e6942e-1c7c-477e-b7da-3ff1e18e4402
[Linux]: https://rustforce.net/article?id=2342532b-c85b-41b3-ad38-62865825bb9b
[MacOS]: https://rustforce.net/article?id=0e8628b4-8d92-40eb-89cd-71ae5cf373ec
[Windows]: https://rustforce.net/article?id=ac93073d-cff7-4c96-bbaf-047eceb4f13d
[Verify Installation 验证安装]: https://rustforce.net/article?id=35d99a70-bfd0-4169-b664-de44129df484
[Getting started 新手入门]: https://rustforce.net/article?id=ccd2cb4c-fd14-4f26-b90b-19aa330b8cca
[QEMU Qemu仿真软件]: https://rustforce.net/article?id=88c8ac37-c31c-495c-8b17-7e12b51a618a
[Hardware 硬件_]: https://rustforce.net/article?id=1c177318-e98e-4ddd-8e3c-5fb48320f912