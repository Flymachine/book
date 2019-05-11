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

> 上一节 
> 1.4. [Installation 安装]
>
> 目录 
> [Index 目录]
>
> 下一节 
> 1.4.4. [Verify Installation 验证安装]
>
> 跳转 
>    - 1.4.1. [Linux]
>    - 1.4.2. [MacOS]
>    - 1.4.3. [Windows]

[Index 目录]: https://rustforce.net/article?id=943af2e7-0f1f-40fd-8864-4bb4d2676b4d
[Tooling 工具]: https://rustforce.net/article?id=07057df5-c646-46ee-bbf6-5ce3c7a131b7
[Linux]: https://rustforce.net/article?id=2342532b-c85b-41b3-ad38-62865825bb9b
[MacOS]: https://rustforce.net/article?id=0e8628b4-8d92-40eb-89cd-71ae5cf373ec
[Windows]: https://rustforce.net/article?id=ac93073d-cff7-4c96-bbaf-047eceb4f13d
[Verify Installation 验证安装]: https://rustforce.net/article?id=35d99a70-bfd0-4169-b664-de44129df484