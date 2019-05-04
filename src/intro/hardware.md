# Meet Your Hardware 认识一下你的硬件

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

让我们熟悉一下接下来要使用的硬件。

## STM32F3DISCOVERY (缩写"F3")

<p align="center">
<img title="F3" src="../assets/f3.jpg">
</p>

这个板子包含什么呢？

- 一块[STM32F303VCT6](https://www.st.com/en/microcontrollers/stm32f303vc.html)单片机. 这种单片机有
  - 一块单核ARM Cortex-M4F处理器，有硬件支持的单精度浮点运算，最大时钟频率为72 MHz。

  - 256 KiB 的Flash存储. (1 KiB = 10**24** bytes)

  - 48 KiB 的RAM.

  - 各种集成外设，比如定时器，I2C，SPI和USART。

  - 通用输入输出（GPIO）和其他类型的引脚，均可通过板旁边的两排排针连接。
  
  - 一个usb接口，可通过标有"USB USER"的USB端口访问。

- 一个[accelerometer 加速度计](https://en.wikipedia.org/wiki/Accelerometer)，是[LSM303DLHC](https://www.st.com/en/mems-and-sensors/lsm303dlhc.html)芯片的一部分。

- 一个[magnetometer 磁力计](https://en.wikipedia.org/wiki/Magnetometer)，也是[LSM303DLHC](https://www.st.com/en/mems-and-sensors/lsm303dlhc.html)芯片的一部分。

- 一个[gyroscope 陀螺仪](https://en.wikipedia.org/wiki/Gyroscope)，是[L3GD20](https://www.pololu.com/file/0J563/L3GD20.pdf)芯片的一部分。

- 8个用户控制的LED灯，以罗盘的形状排列。

- 一块辅助单片机：一块[STM32F103](https://www.st.com/en/microcontrollers/stm32f103cb.html)。这块单片机实际上是板载编程器/调试器的一部分，并连接到名为“USB ST-LINK”的USB端口。

有关开发板功能和更多规格的更详细列表可在[STMicroelectronics](https://www.st.com/en/evaluation-tools/stm32f3discovery.html)网站上查看.

注意事项：如果要将外部信号应用于此开发板，请务必小心。单片机STM32F303VCT6引脚的标称电压为3.3伏。更多细节请查阅[6.2 Absolute maximum ratings section in the manual 单片机手册中的绝对最大额定值部分](https://www.st.com/resource/en/datasheet/stm32f303vc.pdf)

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