# Getting Started 新手入门

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

在本节中，我们将引导你完成编写，构建，刷写和调试嵌入式程序的过程。你将能够在没有任何特殊硬件的情况下尝试大多数示例，因为我们将使用QEMU（一种流行的开源硬件模拟器）向你展示基础知识。
唯一需要硬件的部分自然就是[Hardware 硬件](./hardware.md)部分，我们使用OpenOCD对[STM32F3DISCOVERY]进行编程。

[STM32F3DISCOVERY]: http://www.st.com/en/evaluation-tools/stm32f3discovery.html

> 上一节 
> 1.4.4. [Verify Installation 验证安装]
>
> 目录 
> [Index 目录]
>
> 下一节 
> 2.1. [QEMU Qemu仿真软件]
>

[Index 目录]: https://rustforce.net/article?id=943af2e7-0f1f-40fd-8864-4bb4d2676b4d
[Verify Installation 验证安装]: https://rustforce.net/article?id=35d99a70-bfd0-4169-b664-de44129df484
[QEMU Qemu仿真软件]: https://rustforce.net/article?id=88c8ac37-c31c-495c-8b17-7e12b51a618a
[Hardware 硬件_]: https://rustforce.net/article?id=1c177318-e98e-4ddd-8e3c-5fb48320f912
