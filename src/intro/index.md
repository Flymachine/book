
# Introduction 导言

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

欢迎来到《嵌入式Rust之书》（The Embedded Rust Book）： 一本关于如何在"裸机"嵌入式系统（比如单片机）上使用Rust程序语言的入门书籍。

## Who Embedded Rust is For 嵌入式Rust是为谁开发的

嵌入式Rust是为每一个希望能利用Rust语言提供的高级特性和安全保证来开发嵌入式程序的人而开发的。
(参考 [Who Rust Is For](https://doc.rust-lang.org/book/ch00-00-introduction.html))

## Scope 范围

这本书的目标是:

* 让开发人员快速掌握嵌入式Rust开发。例：如何搭建开发环境。

* 分享*当前*关于Rust嵌入式开发的最佳实践。例：
  如何最好地使用Rust语言特性去写更恰当的嵌入式软件。

* 在某些情况下充当参考书(cookbook)。例：我如何在单个项目里混合使用C和Rust。

本书试图尽可能地提供兼容性，但为了使读者和作者都更容易去做，它在所有的例子中都使用了`ARM Cortex-M`架构。
但是，本书并不假设读者熟悉这种特定的架构，并会在需要时解释该架构的具体细节。

## Who This Book is For 这本书是为谁写的

这本书迎合了一些有些许嵌入式背景或些许Rust背景的人，但我们相信每个对嵌入式Rust编程感兴趣的人都可以从本书中获得一些东西。
对于那些没有任何背景知识的人，我们建议你阅读“Assumptions and Prerequisites 假设和先决条件”部分，然后去了解缺失的知识点，以便从书中获得更多信息并改善你的阅读体验。
你可以查看“Other Resources 其他资源”部分，以查找有关你可能想要了解的主题资源。

### Assumptions and Prerequisites 假设和先决条件

* 你能够自信无忧地使用Rust程序语言，并已经在某个桌面环境里写过、运行过和调试过Rust应用程序。你也应熟悉[2018 版本]的风格，因为这本书对标的是Rust 2018。

[2018 版本]: https://doc.rust-lang.org/edition-guide/

* 你能够自信无忧地使用别的语言（比如C，C++或Ada）开发和调试嵌入式系统，并熟悉以下概念：
  * Cross Compilation 交叉编译
  * Memory Mapped Peripherals 内存映射外围设备（译者注：指的应当是单片机的片内RAM、片外RAM和内存映射概念）
  * Interrupts 中断
  * Common interfaces 通用接口，比如：I2C，SPI，串口等。

### Other Resources 其他资源

如果你不熟悉上述任何内容，或者如果你想了解本书中提到的特定主题的更多信息，你可能会发现某些下面列出的资源会很有用（译者注：资源目前均是指向英文版的）。

| 主题        | 资源 | 描述 |
|--------------|----------|-------------|
| Rust         | [Rust Book](https://doc.rust-lang.org/book/) | 如果你对Rust不熟，我们强烈建议你先阅读这本书。 |
| Rust, Embedded 嵌入式 | [Embedded Rust Bookshelf](https://docs.rust-embedded.org) | 这里你能找到Rust嵌入式工作组的一些其他资源。 |
| Rust, Embedded 嵌入式 | [Embedonomicon](https://docs.rust-embedded.org/embedonomicon/) | 用Rust做嵌入式开发的一些细枝末节。 |
| Rust, Embedded 嵌入式 | [embedded FAQ](https://docs.rust-embedded.org/faq.html) | 在嵌入式背景下有关Rust的常见问题。 |
| Interrupts 中断 | [Interrupt](https://en.wikipedia.org/wiki/Interrupt) | - |
| Memory-mapped IO/Peripherals 内存映射I/O或外设  | [Memory-mapped I/O](https://en.wikipedia.org/wiki/Memory-mapped_I/O) | - |
| SPI, UART, RS232, USB, I2C, TTL | [Stack Exchange about SPI, UART, and other interfaces](https://electronics.stackexchange.com/questions/37814/usart-uart-rs232-usb-spi-i2c-ttl-etc-what-are-all-of-these-and-how-do-th) | - |

## How to Use This Book 如何使用这本书

这本书通常假设你是从头到尾地阅读。后面的章节会基于前面章节中的概念，并且前面的章节可能不会深入研究某个主题的细节，而是在后面的章节中重新讨论该主题。

这本书会在本书包含的大多数例子中使用*STMicroelectronics*出产的 [STM32F3DISCOVERY] 开发板。
这块板子是基于ARM Cortex-M架构的，虽然基于该架构的大多数CPU的基本功能是相同的，但单片机的外围设备和其他实现细节在不同供应商之间是不同的，甚至在同一供应商的不同单片机系列之间通常都是不同的。

因为这个原因，我们建议你采购 [STM32F3DISCOVERY] 开发板以遵循本书中的这些例子。

[STM32F3DISCOVERY]: http://www.st.com/en/evaluation-tools/stm32f3discovery.html

## Contributing to This Book 为本书做贡献

本书的工作在[this repository 本资料库]中进行协调，主要由[resources team]开发。

[this repository 本资料库]: https://github.com/rust-embedded/book
[resources team]: https://github.com/rust-embedded/wg#the-resources-team

如果你无法按照本书中的说明进行操作，或者发现本书的某些部分不够清晰或难以理解，那么这就是一个BUG，你应该在本书的[issue tracker 问题跟踪器]中报告这个BUG。
（译者注：中文翻译的任何问题请发至译者的邮箱：flymachine@foxmail.com，我会尽快修正）

[issue tracker 问题跟踪器]: https://github.com/rust-embedded/book/issues/

非常欢迎你Pull请求以修复拼写错误和添加新内容！

> 上一节 [Index 目录]
>
> 目录 [Index 目录]
>
> 下一节 1.1. [Hardware 硬件]
>

[Index 目录]: https://rustforce.net/article?id=943af2e7-0f1f-40fd-8864-4bb4d2676b4d
[Hardware 硬件]: https://rustforce.net/article?id=e86bd376-948b-4e9e-a359-c419a7206ca1