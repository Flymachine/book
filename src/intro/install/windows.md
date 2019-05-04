# Windows

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

[MIT License]: ./../LICENSE-MIT
[Apache License v2.0]: ./../LICENSE-APACHE
[CC-BY-SA v4.0]: ./../LICENSE-CC-BY-SA
[MIT License Hosted]: https://opensource.org/licenses/MIT
[Apache License v2.0 Hosted]: http://www.apache.org/licenses/LICENSE-2.0
[CC-BY-SA v4.0 Hosted]: https://creativecommons.org/licenses/by-sa/4.0/legalcode

## `arm-none-eabi-gdb`

ARM为Windows提供了`.exe`安装文件。从[这里][gcc]获得，并按照说明操作。
只需要在安装过程完成之前勾选/选择“Add path to environment variable”(添加路径到环境变量)选项。然后验证工具是否在你的`%PATH%`中：

``` console
$ arm-none-eabi-gdb -v
GNU gdb (GNU Tools for Arm Embedded Processors 7-2018-q2-update) 8.1.0.20180315-git
(..)
```

[gcc]: https://developer.arm.com/open-source/gnu-toolchain/gnu-rm/downloads

## OpenOCD

没有官方二进制版本的OpenOCD for Windows，但[这里][openocd]有非官方版本可用。获取0.10.x zip压缩包并将其解压缩到你的驱动器上（我建议使用`C:\OpenOCD`除非你的驱动器号是对你有特殊意义的）然后更新你的`%PATH%`环境变量以包含以下路径：`C:\OpenOCD\bin`（或之前使用的路径）。

[openocd]: https://github.com/gnu-mcu-eclipse/openocd/releases

验证OpenOCD确实在`%PATH%`里可通过：

``` console
$ openocd -v
Open On-Chip Debugger 0.10.0
(..)
```

## QEMU

从[官网][qemu]获取QEMU。

[qemu]: https://www.qemu.org/download/#windows

## ST-LINK USB driver

你也必须安装[这个USB驱动]，否则OpenOCD没法工作。按照安装程序说明操作，确保安装正确的驱动程序版本（32位或64位）。

[这个USB驱动]: http://www.st.com/en/embedded-software/stsw-link009.html

就这些！前往[下一节]吧。

[下一节]: verify.md

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