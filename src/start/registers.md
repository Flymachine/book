# Memory Mapped Registers 内存映射寄存器

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

嵌入式系统到目前为止只能够执行正常的Rust代码，也只能够在内存中移动数据。如果我们想要将任何信息输入或输出我们的系统（无论是使LED闪烁，检测按钮按下还是与某种总线上的片外外设通信），我们将不得不进入这个世界：外设（peripherals）及其“memory mapped registers 内存映射寄存器”。

你可能会发现，访问单片机中的外设（peripherals）所需的代码已经编写完成，具体如下：

* Micro-architecture Crate （微架构库） - 这种crate（译注：同lib，库）处理单片机正在使用的处理器核心常用的所有有用例程，以及使用该特定类型处理器核心的所有单片机所共有的任何外围设备（peripherals）。例如，[cortex-m] crate为你提供启用和禁用中断（interrupts）的功能，这对于所有基于Cortex-M的单片机都是没区别的。它还允许你访问所有基于Cortex-M的单片机所包含的“SysTick 系统定时器”外围设备（peripherals）。
* Peripheral Access Crate (PAC 外设访问库) - 这种crate（译注：同lib，库）是一个瘦包装器（thin wrapper），它覆盖了你正在使用的特定部件号的单片机定义的各种内存封装（memory-wrapper）寄存器。例如，[tm4c123x]用于德州仪器（Texas Instruments）Tiva-C TM4C123系列，或[stm32f30x]用于ST-Micro STM32F30x系列。在这里，你将根据你单片机的技术参考手册中给出的每个外设的操作说明直接与寄存器（peripheral）进行交互。
* HAL Crate （固件库，位于硬件抽象层） - 这些crate（译注：同lib，库）为你的特定处理器提供了更加用户友好的API，通常通过实现[embedded-hal]中定义的一些常见特性。例如，这个crate可能提供一个`Serial`（串口）结构，带有一组构造函数，它采用一组适当的GPIO引脚和一个波特率，并提供某种`write_byte`函数来发送数据。有关[embedded-hal]的更多信息，请参见[Portability 轻量]（译注：暂未翻译到这章）一章。
* Board Crate （电路板库） - 这些crate比HAL Crate更进一步，因为它们能通过预先配置各种外围设备（peripherals）和GPIO引脚以适应你正在使用的特定开发人员套件或电路板，例如用于STM32F3DISCOVERY板的[F3]。

[cortex-m]: https://crates.io/crates/cortex-m
[tm4c123x]: https://crates.io/crates/tm4c123x
[stm32f30x]: https://crates.io/crates/stm32f30x
[embedded-hal]: https://crates.io/crates/embedded-hal
[Portability 轻量]: ../portability/index.md
[F3]: https://crates.io/crates/f3

## Starting at the bottom 从底层开始

让我们来看看所有基于Cortex-M的单片机常见的SysTick（系统定时器）外设。我们可以在[cortex-m] crate（译注：同lib，库）中找到一个非常低级的API，我们可以像这样使用它：

```rust,ignore
use cortex_m::peripheral::{syst, Peripherals};
use cortex_m_rt::entry;

#[entry]
fn main() -> ! {
    let mut peripherals = Peripherals::take().unwrap();
    let mut systick = peripherals.SYST;
    systick.set_clock_source(syst::SystClkSource::Core);
    systick.set_reload(1_000);
    systick.clear_current();
    systick.enable_counter();
    while !systick.has_wrapped() {
        // Loop 循环
    }

    loop {}
}
```

`SYST`结构（struct）上的函数与该外设的ARM技术参考手册（Technical Reference Manual）定义的功能非常接近。这个ARM中没有任何关于“延迟x毫秒”的内容 - 我们必须使用`while`循环粗略地实现它。请注意，在我们调用`Peripherals::take()`之前，我们无法访问`SYST`结构 - 这是一个特殊的例程，它保证整个程序中只有一个`SYST`结构。有关更多信息，请参阅[Peripherals 外围设备]（译注：暂未翻译到这章）部分。

[Peripherals 外围设备]: ../peripherals/index.md

## Using a Peripheral Access Crate (PAC) 使用外设访问库（PAC）

如果我们仅限于每个Cortex-M中包含的基本外围设备（peripherals），我们将无法在嵌入式软件开发方面取得很大进展。在某些时候，我们需要编写一些特定于我们正在使用的特定单片机的代码。在这个例子中，我们假设我们有一个德州仪器（Texas Instruments）TM4C123 - 一个中等的80MHz Cortex-M4单片机，拥有256 KiB的Flash。我们将引入[tm4c123x] crate（译注：同lib，库）来使用这个芯片。

```rust,ignore
#![no_std]
#![no_main]

extern crate panic_halt; // panic handler

use cortex_m_rt::entry;
use tm4c123x;

#[entry]
pub fn init() -> (Delay, Leds) {
    let cp = cortex_m::Peripherals::take().unwrap();
    let p = tm4c123x::Peripherals::take().unwrap();

    let pwm = p.PWM0;
    pwm.ctl.write(|w| w.globalsync0().clear_bit());
    // Mode = 1 => 上升沿/下降沿计数模式
    pwm._2_ctl.write(|w| w.enable().set_bit().mode().set_bit());
    pwm._2_gena.write(|w| w.actcmpau().zero().actcmpad().one());
    // 528 周期 (264/264 上升沿/下降沿) = 4 循环每视频线（video line） (2112 周期)
    pwm._2_load.write(|w| unsafe { w.load().bits(263) });
    pwm._2_cmpa.write(|w| unsafe { w.compa().bits(64) });
    pwm.enable.write(|w| w.pwm4en().set_bit());
}

```

我们访问`PWM0`外设的方式与我们之前访问`SYST`外设的方式完全相同，除了我们调用的是`tm4c123x::Peripherals::take()`。因为这个crate（译注：同lib，库）是使用[svd2rust]自动生成的，所以我们的寄存器字段的访问函数采用闭包，而不是一个数字参数。尽管这看起来像很多代码（a lot of code），但是Rust编译器可以使用它来为我们执行一系列检查，不仅如此之后还能生成与手写汇编程序非常接近的机器代码！其中自动生成的代码无法确定特定访问器函数的所有可能参数是否有效（例如，如果SVD（System View Description XML结构的芯片数据手册）将寄存器定义为32位但未说明某些32位值是否具有特殊意义），所以该功能被标记为“不安全”。当使用`bits()`函数设置`load`和`compa`子字段时，我们可以在其的例子中看到这块内容。

### Reading 读取

`read()`函数返回一个对象，该对象提供对该寄存器中各个子字段的只读访问权限，如该芯片的制造商SVD（System View Description XML结构的芯片数据手册）文件所定义。你可以在[tm4c123x文档] [tm4c123x documentation R]中的特定芯片上找到此特定寄存器的使用特殊`R`返回类型的所有可用函数。

```rust,ignore
if pwm.ctl.read().globalsync0().is_set() {
    // Do a thing
}
```

### Writing 写入

`write()`函数接受一个带有单个参数的闭包（closure）。通常我们称之为`w`。然后，该参数对该寄存器中的各个子字段（sub-fields）进行读写访问，就和该芯片的制造商SVD文件所定义的一样。再次，你可以在[tm4c123x文档] [tm4c123x Documentation W]中的此特定芯片上找到该特定寄存器的'w'上所有可用的功能。请注意，我们未设置的所有子字段（sub-fields）都将被设为默认值 - 寄存器中的任何现有内容都将丢失。

```rust,ignore
pwm.ctl.write(|w| w.globalsync0().clear_bit());
```

### Modifying 修改

如果我们只希望改变该寄存器中的一个特定子字段（sub-fields）并保持其他子字段（sub-fields）不变，我们可以使用`modify`函数。这个函数接受一个带有两个参数的闭包（closure） - 一个用于读取(reading)，一个用于写入(writing)。通常我们分别称它们为`r`和`w`。 `r`参数可用于检查寄存器的当前内容，`w`参数可用于修改寄存器内容。

```rust,ignore
pwm.ctl.modify(|r, w| w.globalsync0().clear_bit());
```

`modify`函数真正显示了闭包（closure）的力量。在C中，我们必须读入一些临时值，修改正确的位然后再写回值。这意味着存在相当大的错误余地（scope for error）：

```C
uint32_t temp = pwm0.ctl.read();
temp |= PWM0_CTL_GLOBALSYNC0;
pwm0.ctl.write(temp);
uint32_t temp2 = pwm0.enable.read();
temp2 |= PWM0_ENABLE_PWM4EN;
pwm0.enable.write(temp); // 哦，不好！错误的变量！
```

[svd2rust]: https://crates.io/crates/svd2rust
[tm4c123x documentation R]: https://docs.rs/tm4c123x/0.7.0/tm4c123x/pwm0/ctl/struct.R.html
[tm4c123x documentation W]: https://docs.rs/tm4c123x/0.7.0/tm4c123x/pwm0/ctl/struct.W.html

## Using a HAL crate 使用HAL crate（固件库）

用于芯片的HAL crate（固件库）通常通过为PAC（外设访问库）暴露的原始结构实现自定义Trait（特征）来工作。通常这个Trait（特征）会为单个外设定义一个名为`constrain()`的函数，或者为比如有复数引脚的GPIO端口定义`split()`。此函数将使用底层原始外设（peripheral）结构并返回具有更高级别API的新对象。这个API也可以做一些事情，比如串口（Serial）`new`函数需要借用一些`Clock`（时钟）结构，这只能通过调用配置PLLs（锁相回路）的函数并设置所有时钟频率来生成。通过这种方式，在没有先配置时钟速率的情况下创建串行（Serial）端口对象，或者串行（Serial）端口对象将波特率错误地转换为时钟节拍（clock ticks）都是绝对不可能发生的。一些crates（译注：同lib，库）甚至为每个GPIO引脚所处的状态定义了特殊特性，要求用户在将引脚传入外设（Peripheral）之前将引脚置于正确状态（例如，通过选择适当的Alternate Function Mode，备用功能模式）。所有这些都没有运行时（run-time）成本！

让我们看一个例子：

```rust,ignore
#![no_std]
#![no_main]

extern crate panic_halt; // panic handler

use cortex_m_rt::entry;
use tm4c123x_hal as hal;
use tm4c123x_hal::prelude::*;
use tm4c123x_hal::serial::{NewlineMode, Serial};
use tm4c123x_hal::sysctl;

#[entry]
fn main() -> ! {
    let p = hal::Peripherals::take().unwrap();
    let cp = hal::CorePeripherals::take().unwrap();

    // 将SYSCTL结构包装到具有更高层API的对象中
    let mut sc = p.SYSCTL.constrain();
    // 选择振荡器（oscillation）设置
    sc.clock_setup.oscillator = sysctl::Oscillator::Main(
        sysctl::CrystalFrequency::_16mhz,
        sysctl::SystemClock::UsePll(sysctl::PllOutputFrequency::_80_00mhz),
    );
    // 使用这些设置配置PLL
    let clocks = sc.clock_setup.freeze();

    // 将GPIO_PORTA结构包装到具有更高层API的对象中。
    // 请注意，它需要borrow（借用）`sc.power_control`，以便它可以自动启动GPIO
    // 外设。
    let mut porta = p.GPIO_PORTA.split(&sc.power_control);

    // 激活UART.
    let uart = Serial::uart0(
        p.UART0,
        // 发送引脚
        porta
            .pa1
            .into_af_push_pull::<hal::gpio::AF1>(&mut porta.control),
        // 接收引脚
        porta
            .pa0
            .into_af_push_pull::<hal::gpio::AF1>(&mut porta.control),
        // 不需要RTS或CTS（硬流控）
        (),
        (),
        // 波特率
        115200_u32.bps(),
        // 输出处理
        NewlineMode::SwapLFtoCRLF,
        // 我们需要时钟频率来计算波特率因子
        &clocks,
        // 我们需要这个来启动UART外设
        &sc.power_control,
    );

    loop {
        writeln!(uart, "Hello, World!\r\n").unwrap();
    }
}
```

> 上一节 2.2. [Hardware 硬件_]
>
> 目录 
> [Index 目录]
>
> 下一节 
> 2.4. Semihosting 半主机(译注：正在翻译中)
>

[Index 目录]: https://rustforce.net/article?id=943af2e7-0f1f-40fd-8864-4bb4d2676b4d
[Hardware 硬件_]: https://rustforce.net/article?id=1c177318-e98e-4ddd-8e3c-5fb48320f912