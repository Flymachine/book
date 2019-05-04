# Tooling 工具

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

处理单片机涉及使用几种不同的工具，因为我们将处理与你的笔记本电脑不同的架构，我们将不得不在*远程*设备上运行和调试程序。

我们将使用下面列出的所有工具。如果未指定最低版本，任何最新版本都应该有效，不过我们已经列出了我们测试过的版本。

- Rust 1.31，1.31-beta，或一个更新的有支持ARM Cortex-M编译的工具链。
- [`cargo-binutils`](https://github.com/rust-embedded/cargo-binutils) ~0.1.4
- [`qemu-system-arm`](https://www.qemu.org/)。 测试过的版本：3.0.0
- OpenOCD >=0.8. 测试过的版本：v0.9.0 和 v0.10.0
- GDB 调试器，支持ARM的。 Version 7.12 或更新的（强烈推荐）。 测试过的版本：7.10，7.11，7.12 和 8.1
- [`cargo-generate`](https://github.com/ashleygwilliams/cargo-generate) or `git`.
  这些工具是可选的，但使用这些可以更容易地跟随本书。

下面的文字解释了我们使用这些工具的原因。安装说明可以在下一页（译者注：下一节[Installation 安装]）找到。

## `cargo-generate` OR `git`

裸机程序是禁用标准库（`no_std`）的Rust程序，需要对链接过程进行一些调整才能使程序的内存布局正确。这需要一些额外的文件（如链接器脚本）和设置（如链接器标志）。我们已经在模板中打包了这些，你只需要填写缺少的信息（例如项目名称和目标硬件的特征）。

我们的模板与`cargo-generate`兼容：一个Cargo子命令，用于从模板创建新的Cargo项目。你也可以使用`git`，`curl`，`wget`或你的网络浏览器下载模板。

## `cargo-binutils`

`cargo-binutils`是Cargo子命令的集合，可以很容易地使用随Rust工具链一起提供的LLVM工具。这些工具包括`objdump`，`nm`和`size`的LLVM版本，用于检查二进制文件。

使用这些工具优于GNU binutils的优点是（a）安装llvm工具是相同的单命令安装（`rustup component add llvm-tools-preview`），与你的操作系统无关，而且（b）工具如`objdump`支持`rustc`支持的所有架构 -- 从arm到x86_64 -- 因为它们共享相同的LLVM后端。

## `qemu-system-arm`

QEMU是一个模拟器（译者注：也称仿真器）。在这种情况下，我们使用可以完全模拟ARM系统的变体。我们使用QEMU在主机上运行嵌入式程序（译者注：此过程在嵌入式行业称之为仿真）。多亏了这一点，即使你没有任何硬件，也可以按照本书的某些部分进行操作！

## GDB 调试器

调试器是嵌入式开发的一个非常重要的组件，因为你可能并不总是可以将内容记录到主机控制台里。在某些情况下，你的硬件上甚至可能没有LED能用来闪烁！

一般来说，LLDB在调试时用起来和GDB差不多，但是我们没有找到与GDB的`load`命令相对应的LLDB命令，这条命令能将程序上传到目标硬件，所以目前我们建议你使用GDB。

## OpenOCD

GDB无法直接与STM32F3DISCOVERY开发板上的ST-Link调试硬件通信。它需要一个翻译器，OpenOCD（Open On-Chip Debugger 开源片上调试器）就是那个翻译器。 OpenOCD是一个在笔记本电脑/PC上运行的程序，它可以把GDB的基于TCP/IP的远程调试协议和ST-Link的基于USB的协议进行相互转换。

OpenOCD还执行其他重要工作，作为其STM32F3DISCOVERY开发板上基于ARM Cortex-M的单片机调试的翻译的一部分：

* 它知道如何与ARM CoreSight调试外设使用的内存映射寄存器进行交互。正是这些CoreSight寄存器允许：
  * 断点/观察点（Breakpoint/Watchpoint）操作
  * 读写CPU寄存器
  * 检测CPU何时因调试事件而停止
  * 遇到调试事件后继续执行CPU
  * 等等。
* 它同样知道如何擦除和写入单片机的FLASH

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