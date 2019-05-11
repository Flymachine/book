# Hardware 硬件

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

[MIT License]: ./LICENSE-MIT
[Apache License v2.0]: ./LICENSE-APACHE
[CC-BY-SA v4.0]: ./LICENSE-CC-BY-SA
[MIT License Hosted]: https://opensource.org/licenses/MIT
[Apache License v2.0 Hosted]: http://www.apache.org/licenses/LICENSE-2.0
[CC-BY-SA v4.0 Hosted]: https://creativecommons.org/licenses/by-sa/4.0/legalcode

到现在为止，您应该对工具和开发过程有所了解。
在本节中，我们将切换到真正的硬件；整个过程其实基本没什么变化。让我们开始吧。

## Know your hardware 了解你的硬件

在开始之前，你需要确认一下目标（target）设备的一些特性，因为这些特性将用于配置项目：

- ARM核心。例如Cortex-M3.

- arm核心是否包含FPU（浮点运算单元）? Cortex-M4**F**和Cortex-M7**F**核心是包含的.

- 目标设备有多少Flash和RAM？例如256 KiB的Flash和32 KiB的RAM.

- Flash和RAM在地址空间中映射的位置在哪里？例如RAM通常位于地址`0x2000_0000`.

你可以在设备的数据手册（Data Sheet）或参考手册（Technical Reference Manual）中找到此信息。

在本节中，我们将使用我们的参考硬件STM32F3DISCOVERY。该板包含一块STM32F303VCT6单片机。这块单片机有：

- 一块Cortex-M4F核心，包含一块单精度FPU（浮点运算单元）。

- 256 KiB的Flash，起始地址0x0800_0000。

- 40 KiB的RAM，起始地址0x2000_0000。（还有另一块RAM区，但为了简单起见，我们将忽略它）

## Configuring 配置

我们将从头开始使用新的模板实例。请参阅[上一节QEMU Qemu仿真软件]，了解如何在不用“cargo-generate”的情况下进行此操作。

[上一节QEMU Qemu仿真软件]: qemu.md

``` console
$ cargo generate --git https://github.com/rust-embedded/cortex-m-quickstart
 Project Name: app
 Creating project called `app`...
 Done! New project created /tmp/app

 $ cd app
```

第一步是在`.cargo/config`中设置默认编译目标。

``` console
$ tail -n5 .cargo/config
```

``` toml
# Pick ONE of these compilation targets
# target = "thumbv6m-none-eabi"    # Cortex-M0 and Cortex-M0+
# target = "thumbv7m-none-eabi"    # Cortex-M3
# target = "thumbv7em-none-eabi"   # Cortex-M4 and Cortex-M7 (no FPU)
target = "thumbv7em-none-eabihf" # Cortex-M4F and Cortex-M7F (with FPU)
```

我们将使用`thumbv7em-none-eabihf`，因为它覆盖了Cortex-M4F核心。

第二步是将内存区域信息（译者注：指起始地址和大小）输入`memory.x`文件。

``` console
$ cat memory.x
/* STM32F303VCT6的链接器脚本 */
MEMORY
{
  /* 注意 1 K = 1 KiB = 1024 bytes */
  FLASH : ORIGIN = 0x08000000, LENGTH = 256K
  RAM : ORIGIN = 0x20000000, LENGTH = 40K
}
```

确保`debug::exit()`调用被注释掉或删除，它仅用于在QEMU中运行时。

```rust,ignore
#[entry]
fn main() -> ! {
    hprintln!("Hello, world!").unwrap();

    // 退出QEMU
    // 注意 不要在硬件上运行它；它会破坏OpenOCD的状态
    // debug::exit(debug::EXIT_SUCCESS);

    loop {}
}
```

你现在可以使用`cargo build`交叉编译程序，并像以前一样使用`cargo-binutils`检查二进制文件。`cortex-m-rt` crate(译者注：Rust术语，指lib，库)可以处理让你的芯片运行所需的所有魔法值(译者注：指莫名其妙出现，很可能是拍脑袋定下来的数值，数值的意义必须通过详细阅读才能推断出来)，还好的是，几乎所有的Cortex-M CPU都是以相同的方式启动。

``` console
$ cargo build --example hello
```

## Debugging 调试

调试会看起来有点不同。实际上，根据目标（target）设备的不同，可能第一步就有区别。在本节中，我们将展示调试在STM32F3DISCOVERY上运行的程序所需的步骤。这里只是作为参考；有关调试的设备特定信息，请参阅[Debugonomicon](https://github.com/rust-embedded/debugonomicon)。

像以前一样，我们将进行远程调试，客户端将是一个GDB进程。但是，这次服务器将是OpenOCD。

在[verify 验证]部分完成时，将Discovery板连接到笔记本电脑/PC并检查是否已连接ST-LINK的排针。

[verify 验证]: ../intro/install/verify.md

在终端上运行`openocd`以连接到Discovery板上的LINK。
从模板的根目录运行此命令; `openocd`将获取`openocd.cfg`文件，该文件指示要使用的接口文件和目标（target）文件。

``` console
$ cat openocd.cfg
```

``` text
# STM32F3DISCOVERY开发板的OpenOCD配置例子。

# 根据你拿到的硬件修订版（revision），你必须选择其中*一*个接口。
# 在任何时候都只应注释掉一个接口。

# 修订版（revision）C（较新的修订版）
source [find interface/stlink-v2-1.cfg]

# 修订版（revision）A和B（较旧的修订版）
# source [find interface/stlink-v2.cfg]

source [find target/stm32f3x.cfg]
```

> **注意** 如果你在[verify 验证]部分发现你有一个较旧版本的Discovery板，那么你
> 应该修改`openocd.cfg`文件，
> 使用`interface/stlink-v2.cfg`。

``` console
$ openocd
Open On-Chip Debugger 0.10.0
Licensed under GNU GPL v2
For bug reports, read
        http://openocd.org/doc/doxygen/bugs.html
Info : auto-selecting first available session transport "hla_swd". To override use 'transport select <transport>'.
adapter speed: 1000 kHz
adapter_nsrst_delay: 100
Info : The selected transport took over low-level target control. The results might differ compared to plain JTAG/SWD
none separate
Info : Unable to match requested speed 1000 kHz, using 950 kHz
Info : Unable to match requested speed 1000 kHz, using 950 kHz
Info : clock speed 950 kHz
Info : STLINK v2 JTAG v27 API v2 SWIM v15 VID 0x0483 PID 0x374B
Info : using stlink api v2
Info : Target voltage: 2.913879
Info : stm32f3x.cpu: hardware has 6 breakpoints, 4 watchpoints
```

在另一个终端上运行GDB，也来自模板的根目录。

``` console
$ <gdb> -q target/thumbv7em-none-eabihf/debug/examples/hello
```

接下来将GDB连接到正在等待端口3333上tcp连接的OpenOCD。

``` console
(gdb) target remote :3333
Remote debugging using :3333
0x00000000 in ?? ()
```

现在使用`load`命令将程序*flash*（加载）到单片机上。

``` console
(gdb) load
Loading section .vector_table, size 0x400 lma 0x8000000
Loading section .text, size 0x1e70 lma 0x8000400
Loading section .rodata, size 0x61c lma 0x8002270
Start address 0x800144e, load size 10380
Transfer rate: 17 KB/sec, 3460 bytes/write.
```

该程序现已加载。这个程序使用半主机（semihosting），所以在我们进行任何半主机（semihosting）调用之前，我们必须告诉OpenOCD启用半主机（semihosting）。你可以使用`monitor`命令向OpenOCD发送命令。

``` console
(gdb) monitor arm semihosting enable
semihosting is enabled
```

> 你可以通过调用`monitor help`命令来查看所有OpenOCD命令。

就像之前我们使用断点（breakpoint）和`continue`命令那样一直跳到`main`。

``` console
(gdb) break main
Breakpoint 1 at 0x8000d18: file examples/hello.rs, line 15.

(gdb) continue
Continuing.
Note: automatically using hardware breakpoints for read-only addresses.

Breakpoint 1, main () at examples/hello.rs:15
15          let mut stdout = hio::hstdout().unwrap();
```

使用`next`推进程序应该产生与以前相同的结果。

``` console
(gdb) next
16          writeln!(stdout, "Hello, world!").unwrap();

(gdb) next
19          debug::exit(debug::EXIT_SUCCESS);
```

在此时你应该看到“Hello, world!”打印在OpenOCD控制台上，还有一些其他内容。

``` console
$ openocd
(..)
Info : halted: PC: 0x08000e6c
Hello, world!
Info : halted: PC: 0x08000d62
Info : halted: PC: 0x08000d64
Info : halted: PC: 0x08000d66
Info : halted: PC: 0x08000d6a
Info : halted: PC: 0x08000a0c
Info : halted: PC: 0x08000d70
Info : halted: PC: 0x08000d72
```

发出另一个`next`将使处理器执行`debug::exit`。这相当于一个断点并停止这个进程：

``` console
(gdb) next

Program received signal SIGTRAP, Trace/breakpoint trap.
0x0800141a in __syscall ()
```

它还会将其打印到OpenOCD控制台：

``` console
$ openocd
(..)
Info : halted: PC: 0x08001188
semihosting: *** application exited ***
Warn : target not halted
Warn : target not halted
target halted due to breakpoint, current mode: Thread
xPSR: 0x21000000 pc: 0x08000d76 msp: 0x20009fc0, semihosting
```

然而，在单片机上运行的进程并没有终止，你可以使用`continue`或类似命令去恢复它。

你现在可以使用`quit`命令退出GDB。

``` console
(gdb) quit
```

现在调试还需要更多的步骤，所以我们将所有这些步骤打包到一个名为`openocd.gdb`的GDB脚本中。

``` console
$ cat openocd.gdb
```

``` text
target remote :3333

# 打印demangled（函数名）符号
set print asm-demangle on

# 检测未处理的异常，硬故障（hard faults）和恐慌（panics，译者注：指严重错误）
break DefaultHandler
break HardFault
break rust_begin_unwind

monitor arm semihosting enable

load

# 启动进程但立即暂停处理器
stepi
```

现在运行`<gdb> -x openocd.gdb $ program`会立即将GDB连接到OpenOCD，启用半主机（semihosting），加载程序并启动进程。

或者，你可以将`<gdb> -x openocd.gdb`转换为自定义运行器，以使`cargo run`可以构建程序*并*启动gdb会话。这个运行器包含在`.cargo/config`中，但它被注释掉了。

``` console
$ head -n10 .cargo/config
```

``` toml
[target.thumbv7m-none-eabi]
# 取消以下注释使得`cargo run`去在QEMU上执行程序
# runner = "qemu-system-arm -cpu cortex-m3 -machine lm3s6965evb -nographic -semihosting-config enable=on,target=native -kernel"

[target.'cfg(all(target_arch = "arm", target_os = "none"))']
# 取消注释以下三个选项之*一*，使`cargo run`去启动GDB会话
# 选择哪个选项取决于你的系统
runner = "arm-none-eabi-gdb -x openocd.gdb"
# runner = "gdb-multiarch -x openocd.gdb"
# runner = "gdb -x openocd.gdb"
```

``` console
$ cargo run --example hello
(..)
Loading section .vector_table, size 0x400 lma 0x8000000
Loading section .text, size 0x1e70 lma 0x8000400
Loading section .rodata, size 0x61c lma 0x8002270
Start address 0x800144e, load size 10380
Transfer rate: 17 KB/sec, 3460 bytes/write.
(gdb)
```

> 上一节 2.1. [QEMU Qemu仿真软件]
>
> 目录 
> [Index 目录]
>
> 下一节 
> 2.3. Memory-mapped Registers 内存映射寄存器(译注：正在翻译中)
>

[Index 目录]: https://rustforce.net/article?id=943af2e7-0f1f-40fd-8864-4bb4d2676b4d
[QEMU Qemu仿真软件]: https://rustforce.net/article?id=88c8ac37-c31c-495c-8b17-7e12b51a618a