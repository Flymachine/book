# Linux

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

这是几个Linux发行版的安装命令。

## Packages 包

- Ubuntu 18.04 或更新 / Debian stretch 或更新

> **注意** `gdb-multiarch`是GDB命令，是你将来调试你的ARM
> Cortex-M程序时用的

<!-- Debian stretch -->
<!-- GDB 7.12 -->
<!-- OpenOCD 0.9.0 -->
<!-- QEMU 2.8.1 -->

<!-- Ubuntu 18.04 -->
<!-- GDB 8.1 -->
<!-- OpenOCD 0.10.0 -->
<!-- QEMU 2.11.1 -->

``` console
sudo apt install gdb-multiarch openocd qemu-system-arm
```

- Ubuntu 14.04 和 16.04

> **注意** `arm-none-eabi-gdb`是GDB命令，是你将来调试你的ARM
> Cortex-M程序时用的

<!-- Ubuntu 14.04 -->
<!-- GDB 7.6 (!) -->
<!-- OpenOCD 0.7.0 (?) -->
<!-- QEMU 2.0.0 (?) -->

``` console
sudo apt install gdb-arm-none-eabi openocd qemu-system-arm
```

- Fedora 27 或更新

> **注意** `arm-none-eabi-gdb`是GDB命令，是你将来调试你的ARM
> Cortex-M程序时用的

<!-- Fedora 27 -->
<!-- GDB 7.6 (!) -->
<!-- OpenOCD 0.10.0 -->
<!-- QEMU 2.10.2 -->

``` console
sudo dnf install arm-none-eabi-gdb openocd qemu-system-arm
```

- Arch Linux

> **注意** `arm-none-eabi-gdb`是GDB命令，是你将来调试你的ARM
> Cortex-M程序时用的

``` console
sudo pacman -S arm-none-eabi-gdb qemu-arch-extra openocd
```

## udev rules udev规则

此规则允许您将OpenOCD与没有root权限的Discovery板一起使用。

创建`/etc/udev/rules.d/70-st-link.rules`文件，包含下述的内容.

``` text
# STM32F3DISCOVERY rev A/B - ST-LINK/V2
ATTRS{idVendor}=="0483", ATTRS{idProduct}=="3748", TAG+="uaccess"

# STM32F3DISCOVERY rev C+ - ST-LINK/V2-1
ATTRS{idVendor}=="0483", ATTRS{idProduct}=="374b", TAG+="uaccess"
```

然后使用下面的命令重新载入所有的udev规则：

``` console
sudo udevadm control --reload-rules
```

如果您将板子已经插在笔记本电脑上了，请将其拔下然后重新插上。

您可以通过运行此命令来检查权限：

``` console
lsusb
```

这应该会显示和下面类似的东西

```text
(..)
Bus 001 Device 018: ID 0483:374b STMicroelectronics ST-LINK/V2.1
(..)
```

记下bus和device号。使用这些号来创建一个类似的路径
`/dev/bus/usb/<bus>/<device>`。 然后像这样使用路径:

``` console
ls -l /dev/bus/usb/001/018
```

```text
crw-------+ 1 root root 189, 17 Sep 13 12:34 /dev/bus/usb/001/018
```

```console
getfacl /dev/bus/usb/001/018 | grep user
```

```text
user::rw-
user:you:rw-
```

附加到权限后的`+`表示存在扩展权限。 `getfacl`命令告诉用户`you`可以使用这个设备。

现在，可以去[下一节]了.

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