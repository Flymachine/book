# Verify Installation 验证安装

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

在本节中，我们将检查是否已正确安装和配置了一些必需的工具/驱动程序。

使用micro USB线将笔记本电脑/PC连接到Discovery板。Discovery板有两个USB连接器;使用标有“USB ST-LINK”的那个，它位于板边的中央。

还要检查是否已连接USB ST-LINK排针。见下图; USB ST-LINK排针用红线圈出。

<p align="center">
<img title="Connected discovery board" src="../../assets/verify.jpeg">
</p>

现在运行以下命令：

``` console
$ openocd -f interface/stlink-v2-1.cfg -f target/stm32f3x.cfg
```

你应该得到以下输出，这个程序应会阻塞控制台：

``` text
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
Info : Target voltage: 2.919881
Info : stm32f3x.cpu: hardware has 6 breakpoints, 4 watchpoints
```

内容可能不会完全匹配，但你应该获得的最后一行是关于breakpoints和watchpoints（断点和观察点）的。如果你得到它了接下来就可以终止OpenOCD进程并转到[下一节]。

[下一节]: ../hardware.md

如果你没有拿到"breakpoints"一行，之后尝试一下以下命令。

``` console
$ openocd -f interface/stlink-v2.cfg -f target/stm32f3x.cfg
```

如果该命令有效，则意味着你拿到的是Discovery板的旧硬件版本。这不会是一个问题，但会将这个实际情况提交给内存，因为你以后需要稍微改变一下。你可以转到[下一节]。

如果两个命令都是用普通用户运行的，那么尝试使用root权限运行它们（例如`sudo openocd ..`）。如果命令全部是用root权限运行的，则检查[udev rules udev规则]是否已正确设置。

[udev rules udev规则]: linux.md#udev-rules

如果你已经试过了上述方式并且OpenOCD仍无法正常工作，请提交[一个问题]，我们会帮助你的！

[一个问题]: https://github.com/rust-embedded/book/issues

> 上一节 
> 1.4.3. [Windows]
>
> 目录 
> [Index 目录]
>
> 下一节 2. [Getting started 新手入门]
>
> 跳转 
>    - 1.4.1. [Linux]
>    - 1.4.2. [MacOS]
>    - 1.4.3. [Windows]

[Index 目录]: https://rustforce.net/article?id=943af2e7-0f1f-40fd-8864-4bb4d2676b4d
[Linux]: https://rustforce.net/article?id=2342532b-c85b-41b3-ad38-62865825bb9b
[MacOS]: https://rustforce.net/article?id=0e8628b4-8d92-40eb-89cd-71ae5cf373ec
[Windows]: https://rustforce.net/article?id=ac93073d-cff7-4c96-bbaf-047eceb4f13d
[Getting started 新手入门]: https://rustforce.net/article?id=ccd2cb4c-fd14-4f26-b90b-19aa330b8cca