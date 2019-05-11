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

> 上一节  1. [Introduction 导言]
>
> 目录 
> [Index 目录]
>
> 下一节 
> 1.2. [no_std 禁用标准库]
>

[Introduction 导言]: https://rustforce.net/article?id=8da8d5bf-f2af-4c9a-b3f3-567c19488871
[Index 目录]: https://rustforce.net/article?id=943af2e7-0f1f-40fd-8864-4bb4d2676b4d
[no_std 禁用标准库]: https://rustforce.net/article?id=5697689c-b8f1-4144-88fa-6f9f45ed7f83