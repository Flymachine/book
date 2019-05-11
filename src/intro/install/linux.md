# Linux

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

这是几个Linux发行版的安装命令。

## Packages 包

- Ubuntu 18.04 或更新 / Debian stretch 或更新

> **注意** `gdb-multiarch`是GDB命令，是你将来调试你的ARM
> Cortex-M程序时用的

<!-- Debian stretch -->
<!-- GDB 7.12 -->
<!-- OpenOCD 0.9.0 -->
<!-- QEMU 2.8.1 -->

<!-- Ubuntu 18.04 -->
<!-- GDB 8.1 -->
<!-- OpenOCD 0.10.0 -->
<!-- QEMU 2.11.1 -->

``` console
sudo apt install gdb-multiarch openocd qemu-system-arm
```

- Ubuntu 14.04 和 16.04

> **注意** `arm-none-eabi-gdb`是GDB命令，是你将来调试你的ARM
> Cortex-M程序时用的

<!-- Ubuntu 14.04 -->
<!-- GDB 7.6 (!) -->
<!-- OpenOCD 0.7.0 (?) -->
<!-- QEMU 2.0.0 (?) -->

``` console
sudo apt install gdb-arm-none-eabi openocd qemu-system-arm
```

- Fedora 27 或更新

> **注意** `arm-none-eabi-gdb`是GDB命令，是你将来调试你的ARM
> Cortex-M程序时用的

<!-- Fedora 27 -->
<!-- GDB 7.6 (!) -->
<!-- OpenOCD 0.10.0 -->
<!-- QEMU 2.10.2 -->

``` console
sudo dnf install arm-none-eabi-gdb openocd qemu-system-arm
```

- Arch Linux

> **注意** `arm-none-eabi-gdb`是GDB命令，是你将来调试你的ARM
> Cortex-M程序时用的

``` console
sudo pacman -S arm-none-eabi-gdb qemu-arch-extra openocd
```

## udev rules udev规则

此规则允许你将OpenOCD与没有root权限的Discovery板一起使用。

创建`/etc/udev/rules.d/70-st-link.rules`文件，包含下述的内容.

``` text
# STM32F3DISCOVERY rev A/B - ST-LINK/V2
ATTRS{idVendor}=="0483", ATTRS{idProduct}=="3748", TAG+="uaccess"

# STM32F3DISCOVERY rev C+ - ST-LINK/V2-1
ATTRS{idVendor}=="0483", ATTRS{idProduct}=="374b", TAG+="uaccess"
```

然后使用下面的命令重新载入所有的udev规则：

``` console
sudo udevadm control --reload-rules
```

如果你将板子已经插在笔记本电脑上了，请将其拔下然后重新插上。

你可以通过运行此命令来检查权限：

``` console
lsusb
```

这应该会显示和下面类似的东西

```text
(..)
Bus 001 Device 018: ID 0483:374b STMicroelectronics ST-LINK/V2.1
(..)
```

记下bus和device号。使用这些号来创建一个类似的路径
`/dev/bus/usb/<bus>/<device>`。 然后像这样使用路径:

``` console
ls -l /dev/bus/usb/001/018
```

```text
crw-------+ 1 root root 189, 17 Sep 13 12:34 /dev/bus/usb/001/018
```

```console
getfacl /dev/bus/usb/001/018 | grep user
```

```text
user::rw-
user:you:rw-
```

附加到权限后的`+`表示存在扩展权限。 `getfacl`命令告诉用户`you`可以使用这个设备。

现在，可以去[下一节]了.

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