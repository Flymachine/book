# Installing the tools 工具安装

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

此页面包含一些工具的跨系统的（OS-agnostic）安装说明：

### Rust Toolchain Rust工具链

安装rustup请依照放在[https://rustup.rs](https://rustup.rs)上的说明。

**注意** 确保你的编译器版本等于或高于`1.31`。`rustc -v`应该返回比下面显示的日期更新的日期。

``` console
$ rustc -V
rustc 1.31.1 (b6c32da9b 2018-12-18)
```

出于对带宽和磁盘使用的考虑，默认安装仅支持本机编译。选择以下编译目标中的一个以为ARM Cortex-M架构添加交叉编译支持。对于用于本书示例的STM32F3DISCOVERY板，请使用最后的`thumbv7em-none-eabihf`目标。

Cortex-M0, M0+, 和 M1 (ARMv6-M架构):
``` console
$ rustup target add thumbv6m-none-eabi
```

Cortex-M3 (ARMv7-M架构):
``` console
$ rustup target add thumbv7m-none-eabi
```

Cortex-M4 和 M7，没有浮点运算单元 (ARMv7E-M架构):
``` console
$ rustup target add thumbv7em-none-eabi
```

Cortex-M4F 和 M7F，有浮点运算单元 (ARMv7E-M架构):
``` console
$ rustup target add thumbv7em-none-eabihf
```

### `cargo-binutils`

``` console
$ cargo install cargo-binutils

$ rustup component add llvm-tools-preview
```

### OS-Specific Instructions 特定系统的说明

现在按照你正在使用的操作系统的具体说明进行操作:

- [Linux](install/linux.md)
- [Windows](install/windows.md)
- [macOS](install/macos.md)

> 上一节 
> 1.3. [Tooling 工具]
>
> 目录 
> [Index 目录]
>
> 下一节 
> 1.4.1. [Linux]
>
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