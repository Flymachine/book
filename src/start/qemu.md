# QEMU Qemu仿真软件

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

我们将从为[LM3S6965]——一款Cortex-M3单片机编写程序开始。
我们选择这个作为我们得初始目标（target）是因为它[可以使用QEMU模拟](https://wiki.qemu.org/Documentation/Platforms/ARM#Supported_in_qemu-system-arm)这样你就不需要在这节就摆弄硬件，我们也可以把精力集中在工具和开发过程上。

[LM3S6965]: http://www.ti.com/product/LM3S6965

## Creating a non standard Rust program 创建一个禁用标准库的Rust程序

我们将使用[`cortex-m-quickstart`]项目模板去生成一个基于它的新项目。

[`cortex-m-quickstart`]: https://github.com/rust-embedded/cortex-m-quickstart

- 使用`cargo-generate`

```console
cargo generate --git https://github.com/rust-embedded/cortex-m-quickstart
```

```text
 Project Name: app
 Creating project called `app`...
 Done! New project created /tmp/app
```

```console
cd app
```

- 使用`git`

Clone这个仓库

```console
git clone https://github.com/rust-embedded/cortex-m-quickstart app
cd app
```

然后填写`Cargo.toml`文件中的占位符

```toml
[package]
authors = ["{{authors}}"] # "{{authors}}" -> "John Smith"
edition = "2018"
name = "{{project-name}}" # "{{project-name}}" -> "awesome-app"
version = "0.1.0"

# ..

[[bin]]
name = "{{project-name}}" # "{{project-name}}" -> "awesome-app"
test = false
bench = false
```

- 也可以这样用

抓取`cortex-m-quickstart`模板的最新快照并提取它。

使用以下命令行：

```console
curl -LO https://github.com/rust-embedded/cortex-m-quickstart/archive/master.zip
unzip master.zip
mv cortex-m-quickstart-master app
cd app
```

或者你也能用浏览器打开[`cortex-m-quickstart`]，点击绿色“Clone or
download”按钮，然后点击“Download ZIP”。

之后填写`Cargo.toml`文件中的占位符就和“使用`git`”版本的第二部分一样。

**重要** 我们将在本教程中使用名称“app”作为项目名称（project-name）。每当你看到“app”一词时，你应该将其替换为你为项目选择的名称。或者，你也可以将项目命名为“app”并避免替换。

为方便起见，这里展示一下`src/main.rs`中源代码最重要的部分：

```rust,ignore
#![no_std]
#![no_main]

extern crate panic_halt;

use cortex_m_rt::entry;

#[entry]
fn main() -> ! {
    loop {
        // your code goes here
    }
}
```

这个程序与标准的Rust程序有点不同，所以让我们仔细看看。

`#![no_std]`表示该程序*不会*链接到标准crate（译者注：Rust术语，等同于lib，库）——`std`。相反，它将链接到`std`的子集：`core` crate（译者注：即核心库）。

`#![no_main]`表示该程序不会使用大多数Rust程序使用的标准`main`接口。使用`no_main`的主要（main，不是双关语）原因是使用`no_std`上下文中的`main`接口需要nightly版本。

`extern crate panic_halt;`。这个crate提供了一个“panic_handler”来定义程序的恐慌（panicking，译者注：指发生严重错误）行为。我们将在本书的[Panicking 恐慌]（panicking.md）章节中更详细地介绍这一点。

[`#[entry]`](https://docs.rs/cortex-m-rt-macros/latest/cortex_m_rt_macros/attr.entry.html)是由[`cortex-m-rt`](https://crates.io/crates/cortex-m-rt) crate提供的属性，用于标记程序的入口点。因为我们没有使用标准的`main`接口，所以我们需要另一种方式来指示程序的入口点，那就是`#[entry]`。

`fn main() -> !`。我们的程序将是目标硬件上运行的*唯一*进程，因此我们不希望它结束​​！我们使用[divergent function 发散函数](https://doc.rust-lang.org/rust-by-example/fn/diverging.html)（在函数签名处加`-> !`） 来确保在编译时程序确实不会结束。

### Cross compiling 交叉编译

下一步是*交叉*编译Cortex-M3架构的程序。如果你知道编译目标（`$TRIPLE`）应该是什么，那就像运行`cargo build --target $TRIPLE`一样简单。幸运的是，模板中的`.cargo/config`有这个答案：

```console
tail -n6 .cargo/config
```

```toml
[build]
# Pick ONE of these compilation targets
# target = "thumbv6m-none-eabi"    # Cortex-M0 and Cortex-M0+
target = "thumbv7m-none-eabi"    # Cortex-M3
# target = "thumbv7em-none-eabi"   # Cortex-M4 and Cortex-M7 (no FPU 无浮点运算单元)
# target = "thumbv7em-none-eabihf" # Cortex-M4F and Cortex-M7F (with FPU 有浮点运算单元)
```

要交叉编译Cortex-M3架构，我们必须使用`thumbv7m-none-eabi`。此编译目标已设置为默认值，因此下面的两个命令会执行相同的操作：

```console
cargo build --target thumbv7m-none-eabi
cargo build
```

### Inspecting 检查

现在我们在`target/thumbv7m-none-eabi/debug/app`中有一个非本地用的（non-native）ELF二进制文件。我们可以使用`cargo-binutils`来检查它。

使用`cargo-readobj`我们可以打印出ELF标头以确认这是一个ARM二进制文件。

``` console
cargo readobj --bin app -- -file-headers
```

注意这些:
* `--bin app`是用于检查`target/$TRIPLE/debug/app`处二进制文件的语法糖
* `--bin app`如有必要，还将（重新）编译二进制文件

``` text
ELF Header:
  Magic:   7f 45 4c 46 01 01 01 00 00 00 00 00 00 00 00 00
  Class:                             ELF32
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0x0
  Type:                              EXEC (Executable file)
  Machine:                           ARM
  Version:                           0x1
  Entry point address:               0x405
  Start of program headers:          52 (bytes into file)
  Start of section headers:          153204 (bytes into file)
  Flags:                             0x5000200
  Size of this header:               52 (bytes)
  Size of program headers:           32 (bytes)
  Number of program headers:         2
  Size of section headers:           40 (bytes)
  Number of section headers:         19
  Section header string table index: 18
```

`cargo-size`能够打印出二进制文件的链接器（linker）部分的大小。

> **注意** 此输出假定已嵌入了rust-embedded/cortex-m-rt#111

```console
cargo size --bin app --release -- -A
```

我们使用`--release`去检查优化版本

``` text
app  :
section             size        addr
.vector_table       1024         0x0
.text                 92       0x400
.rodata                0       0x45c
.data                  0  0x20000000
.bss                   0  0x20000000
.debug_str          2958         0x0
.debug_loc            19         0x0
.debug_abbrev        567         0x0
.debug_info         4929         0x0
.debug_ranges         40         0x0
.debug_macinfo         1         0x0
.debug_pubnames     2035         0x0
.debug_pubtypes     1892         0x0
.ARM.attributes       46         0x0
.debug_frame         100         0x0
.debug_line          867         0x0
Total              14570
```

> ELF链接器（linker）部分的回顾
>
> - `.text`包含程序指令
> - `.rodata`包含字符串等常量值
> - `.data`包含静态分配的变量，其初始值为*不为*0
> - `.bss`还包含静态分配的变量，其初始值*为*0
> - `.vector_table`是一个*非*标准的部分，我们用它来存储vector（中断）表
> - `.ARM.attributes`和`.debug_*`部分包含元数据，并且在刷写二进制文件时*不会*加载到目标上。

**重要**: ELF文件包含调试信息之类的元数据，因此它们*在磁盘上的体积大小*并*不能*准确反映程序刷写到设备上时占用的空间。请*总是*使用`cargo-size`来检查二进制文件的真实大小。

`cargo-objdump`可以用来反汇编二进制文件。

```console
cargo objdump --bin app --release -- -disassemble -no-show-raw-insn -print-imm-hex
```

> **注意** 此输出在你的系统上可能有所不同。新版本的rustc，LLVM和库可以生成不同的程序集。我们截断了一些说明，以使这个代码段变小。

```text
app:  file format ELF32-arm-little

Disassembly of section .text:
main:
     400: bl  #0x256
     404: b #-0x4 <main+0x4>

Reset:
     406: bl  #0x24e
     40a: movw  r0, #0x0
     < .. truncated any more instructions .. >

DefaultHandler_:
     656: b #-0x4 <DefaultHandler_>

UsageFault:
     657: strb  r7, [r4, #0x3]

DefaultPreInit:
     658: bx  lr

__pre_init:
     659: strb  r7, [r0, #0x1]

__nop:
     65a: bx  lr

HardFaultTrampoline:
     65c: mrs r0, msp
     660: b #-0x2 <HardFault_>

HardFault_:
     662: b #-0x4 <HardFault_>

HardFault:
     663: <unknown>
```

### Running 运行

接下来，让我们看看如何在QEMU上运行嵌入式程序！这次我们将使用实实在在做些事情的`hello`示例。

为方便起见，这里是`examples/hello.rs`的源代码：

```rust,ignore
//! 打印"Hello, world!"到主机控制台上，通过半主机（semihosting）的方式

#![no_main]
#![no_std]

extern crate panic_halt;

use cortex_m_rt::entry;
use cortex_m_semihosting::{debug, hprintln};

#[entry]
fn main() -> ! {
    hprintln!("Hello, world!").unwrap();

    // 退出QEMU
    // 注意 不要在硬件上运行它；它会破坏OpenOCD的状态
    debug::exit(debug::EXIT_SUCCESS);

    loop {}
}
```

此程序使用称为半主机（semihosting）的东西将文本打印到*主机*控制台。当使用真实硬件时，这需要一个调试会话，但是当使用QEMU时，仅现在这样就可以起作用了。

让我们从编译这个例子开始：

```console
cargo build --example hello
```

输出的二进制文件会放在`target/thumbv7m-none-eabi/debug/examples/hello`下。

为了在QEMU中运行这个二进制文件，需要执行以下命令：

```console
qemu-system-arm \
  -cpu cortex-m3 \
  -machine lm3s6965evb \
  -nographic \
  -semihosting-config enable=on,target=native \
  -kernel target/thumbv7m-none-eabi/debug/examples/hello
```

```text
Hello, world!
```

打印文本后，命令应成功退出（exit code = 0 退出状态码为0）。在*nix上，你可以使用以下命令检查：

```console
echo $?
```

```text
0
```

让我们分解一下QEMU命令：

- `qemu-system-arm`。这是QEMU模拟器执行文件。这些QEMU二进制文件有一些变种；这个名称代表会全*系统*模拟*ARM*机器。

- `-cpu cortex-m3`。这告诉QEMU模拟一个Cortex-M3 CPU。指定CPU模型能让我们捕获一些错误编译错误：例如，运行为Cortex-M4F编译的程序（具有硬件FPU，浮点运算单元）将在执行期间产生QEMU错误。

- `-machine lm3s6965evb`。这告诉QEMU模拟LM3S6965EVB（这是一种包含一块LM3S6965单片机的评估板）。

- `-nographic`。这告诉QEMU不要启动它的GUI。

- `-semihosting-config (..)`。这告诉qemu启用半主机（semihosting）。半主机允许模拟设备使用主机的stdout，stderr和stdin以及在主机上创建文件。

- `-kernel $file`。这告诉QEMU在模拟机器上加载和运行哪个二进制文件。

输入那个长长的QEMU命令实在是太烦了！我们可以设置自定义运行器来简化流程。`.cargo/config`有一个注释掉的运行器能调用QEMU；让我们取消它：

```console
head -n3 .cargo/config
```

```toml
[target.thumbv7m-none-eabi]
# uncomment this to make `cargo run` execute programs on QEMU
runner = "qemu-system-arm -cpu cortex-m3 -machine lm3s6965evb -nographic -semihosting-config enable=on,target=native -kernel"
```

此运行器仅适用于`thumbv7m-none-eabi`目标，这是我们的默认编译目标。现在`cargo run`将编译程序并在QEMU上运行它：

```console
cargo run --example hello --release
```

```text
   Compiling app v0.1.0 (file:///tmp/app)
    Finished release [optimized + debuginfo] target(s) in 0.26s
     Running `qemu-system-arm -cpu cortex-m3 -machine lm3s6965evb -nographic -semihosting-config enable=on,target=native -kernel target/thumbv7m-none-eabi/release/examples/hello`
Hello, world!
```

### Debugging 调试

调试对嵌入式开发至关重要。让我们看看它是如何完成的。

调试嵌入式设备需要*远程*调试，因为我们要调试的程序不会在正运行调试程序（GDB or LLDB）的机器上运行。

远程调试涉及客户端和服务器。在QEMU设置中，客户端将是一个GDB（或LLDB）进程，服务器将是QEMU进程，它也在运行嵌入式程序。

在本节中，我们将使用我们已编译的`hello`示例。

第一个调试步骤是在调试模式下启动QEMU：

```console
qemu-system-arm \
  -cpu cortex-m3 \
  -machine lm3s6965evb \
  -nographic \
  -semihosting-config enable=on,target=native \
  -gdb tcp::3333 \
  -S \
  -kernel target/thumbv7m-none-eabi/debug/examples/hello
```

此命令不会向控制台打印任何内容，并将阻塞这个终端。这次我们使用了两个额外的标志：

- `-gdb tcp::3333`。这告诉QEMU在TCP端口3333上等待GDB连接。

- `-S`。这告诉QEMU在启动时冻结（freeze）机器。如果没有这个程序，在我们有机会启动调试器之前程序就会跑到main的末尾！

接下来我们在另一个终端中启动GDB并告诉它加载示例的调试符号：

```console
gdb-multiarch -q target/thumbv7m-none-eabi/debug/examples/hello
```

**注意**：你可能需要另一个版本的gdb而不是`gdb-multiarch`，具体取决于你在安装章节中安装的是哪个。这里也可能是`arm-none-eabi-gdb`或者只是`gdb`。

然后在GDB shell中我们连接到QEMU，它正在等待TCP端口3333上的连接。

```console
target remote :3333
```

```text
Remote debugging using :3333
Reset () at $REGISTRY/cortex-m-rt-0.6.1/src/lib.rs:473
473     pub unsafe extern "C" fn Reset() -> ! {
```

你会看到进程暂停（halted），程序计数器指向一个名为`Reset`的函数。这是重置（Reset）处理程序：在启动时Cortex-M核心在执行什么。

这个重置处理程序最终会调用我们的main函数。让我们使用一个断点和`continue`命令来把那里全跳过去：

```console
break main
```

```text
Breakpoint 1 at 0x400: file examples/panic.rs, line 29.
```

```console
continue
```

```text
Continuing.

Breakpoint 1, main () at examples/hello.rs:17
17          let mut stdout = hio::hstdout().unwrap();
```

我们现在接近用来打印“hello，world！”的代码。让我们使用`next`命令继续前进。

``` console
next
```

```text
18          writeln!(stdout, "Hello, world!").unwrap();
```

```console
next
```

```text
20          debug::exit(debug::EXIT_SUCCESS);
```

在这时你应该能看到“Hello, world!”打印在运行`qemu-system-arm`的终端上。

```text
$ qemu-system-arm (..)
Hello, world!
```

再次调用`next`将终止QEMU进程。

```console
next
```

```text
[Inferior 1 (Remote target) exited normally]
```

你现在可以退出GDB会话了。

``` console
quit
```

> 上一节 2. [Getting started 新手入门]
>
> 目录 
> [Index 目录]
>
> 下一节 
> 2.2. [Hardware 硬件_]
>

[Index 目录]: https://rustforce.net/article?id=943af2e7-0f1f-40fd-8864-4bb4d2676b4d
[Getting started 新手入门]: https://rustforce.net/article?id=ccd2cb4c-fd14-4f26-b90b-19aa330b8cca
[Hardware 硬件_]: https://rustforce.net/article?id=1c177318-e98e-4ddd-8e3c-5fb48320f912