# 瑞萨 CPK-RA6M4 开发板 BSP 说明

## 简介
[MCUboot](https://github.com/mcu-tools/mcuboot) 是用于 32 位微控制器的安全引导加载程序。它为微控制器系统上的引导加载程序和系统闪存布局定义了通用基础结构，并提供了一个安全的引导加载程序，可实现轻松的软件升级。本工程是将mcuboot移植至RT-Thread，并运行在CPK-RA6M4开发板上的示例工程。

## 升级方式

mcuboot 支持多种升级方式，如 overwrite swap等，这里选用overwrite作为升级方式，mcuboot 会将 app镜像文件进行安全校验后，从 image0 secondary SLOT 搬运至 image0 primary SLOT，然后跳转。

## flash分区

~~~
┌───────────────────────────────────┐ 0x0104 8510
│              ......               │
├───────────────────────────────────┤ 0x00100000
│       image0 secondary SLOT       │
│                                   │
│               480KB               │
├───────────────────────────────────┤ 0x00088000
│       image0 primary SLOT         │
│                                   │
│               480KB               │
├───────────────────────────────────┤ 0x00010000
│       FLASH_AREA_BOOTLOADER       │
│                                   │
│               64KB                │
└───────────────────────────────────┘ 0x00000000
~~~

* 注：这里只是列出了当前示例工程中用到的flash的分区信息，更详细的内容可以在 {project}/ra_cfg/mcu-tools/include/sysflash/sysflash.h 中查看

## 镜像签名
由于mcuboot在boot的阶段会对镜像进行安全校验，因此需要对APP的镜像文件签名。
将下面命令的app_dir换成实际的APP路径即可
~~~
python .\ra\fsp\src\rm_mcuboot_port\rm_mcuboot_port_sign.py sign --header-size 0x200 --align 128 --max-align 128 --slot-size 0x78000 --max-sectors 15 --overwrite-only --confirm --pad-header app_dir\rtthread.elf app_dir\rtthread.signed.bin
~~~

## 下载烧录
将镜像签名后下载至 image0 secondary SLOT 的起始地址（0x00088000）即可

![mcuboot](docs/picture/mcuboot.png)

## 联系人信息

在使用过程中若您有任何的想法和建议，建议您通过以下方式来联系到我们  [RT-Thread 社区论坛](https://club.rt-thread.org/)

## 贡献代码

如果您对 CPK-RA6M4 感兴趣，并且有一些好玩的项目愿意与大家分享的话欢迎给我们贡献代码，您可以参考 [如何向 RT-Thread 代码贡献](https://www.rt-thread.org/document/site/#/rt-thread-version/rt-thread-standard/development-guide/github/github)。
