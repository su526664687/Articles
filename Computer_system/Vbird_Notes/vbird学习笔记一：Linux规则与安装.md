---
title: vbird学习笔记一：Linux规则与安装
date: 2017-03-02 00:01:01
categories:
- Computer_system
- Vbird_Notes
tags:
- Linux
description: 《鸟哥的Linux私房菜》学习笔记
---

## 名词解析
* **SELinux**: Security-Enhanced Linux.一种访问控制体系，在这种访问控制体系的限制下，进程只能访问那些在他的任务中所需要文件SELinux 默认安装在 Fedora 和 Red Hat Enterprise Linux 上，也可以作为其他发行版上容易安装的包得到.
* 昆山科技大学: 位于台南市永康区大湾里即古称“鲫鱼潭”故址，鸟哥现在教学的地方.
* 寄存器的物理成分是D触发器，cache的物理成分是SRAM，主存的物理成分是DRAM；SRAM的物理成分是CMOS, DRAM的物理成分是晶体管加电容；CMOS是场效应管的一种；场效应管属于晶体管.
* **PIN**: 外接电源中, PIN其实就是针脚，6PIN那就是6个针脚的

## Unit 0 计算机概述
* 综述
  * **计算机硬件的五大单元**：运算器、控制器、存储器、输入设备、输出设备
  * **控制存储器**: 一条机器指令往往分成几步执行，将每一步操作所需的若干位命令以代码形式编写在一条微指令中，若干条微指令组成一段微程序，对应一条机器指令。在设计CPU时，根据指令系统的需要，事先编制好各段微程序，且将它们存入一个专用存储器（称为控制存储器）中.
  * **指令**: 汇编语言的每条语句即汇编指令(助记符)都和机器指令(机器码)一一对应  
    微程序: 每条机器指令(汇编指令) 都由一个微程序组成，这个微程序包含多条微指令，实质是若干位信号的编码  
    可参考《东北大学计算机组成原理课程设计》<font color="blue">http://wenku.baidu.com/link?url=2zK-TRbQzCLJhfaZp9MiA6tgiEJE2p7b8q5_eph7lYbdTe9wJkNzLtOB7hEL7UkoJf8_KJfudf9GGxQKXXqY4JKqfPbzJwOiH1KAQOKAm5S</font>
* 计算机架构和接口设备
  * **不同的x86架构cpu的区别**: 主要在于微指令集的不同，新的x86的cpu大多包含许多先进的微指令集
  * **Intel主板架构**: 整个主板上面最重要的就是芯片组了!而芯片组通常又分为两个网桥来控制各组件的沟通, 分别是: (1)北桥: 负责链接速度较快的CPU、 主存储器与显示适配器等组件（CPU/RAM/VGA）;(2)南桥:负责连接速度较慢的周边接口, 包括硬盘、USB、网络卡等等（PCI 接口与速度较慢的 I/O 装置）
  * **AMD主板架构**: Intel不同的地方在于主存储器是直接与CPU沟通而不透过北桥，AMD 为了加速这两者的沟通,所以将内存控制组件整合到 CPU 当中, 理论上这样可以加速 CPU 不主存储器的传输速（关于AMD和intel的cpu在架构上的主要差异点也正是这样）
  * **频率、外频、倍频**: 频率是指CPU每秒钟可以进行的工作次数；外频指的是 CPU 与外部组件进行数据传输时的速度,倍频则是 CPU 内部用来加速工作效能的一个倍数, 两者相乘才是 CPU 的频率速度
  * **32位和64位**: 一般主板芯片组有分北桥与南桥, 北桥癿总线称为系统总线,因为是内存传输癿主要信道,所以速度较快；南桥就是所谓的输入输出(I/O)总线,主要在联系硬盘、USB、网络卡等接口设备。北桥每次传送的位数就是总线宽度。现在所称的计算机是 32 或 64 位主要是依据CPU 解析的字组大小而来的，即通用寄存器的位宽.
  * **CPU等级**: i386，i586，i686是根据不同时代对CPU的规范等级，目前的64位CPU统称为x86_64等级.
  * **DDR内存**: 双倍数据传送速度(Double Data Rate), 它可以在一次工作周期中进行两次数据的传送,感觉上就好像是 CPU 的倍频.
  * **双通道**: 传统的总线宽度一般大约仅达 64 位,为了要加大这个宽度,因此芯片组厂商就将两个主存储器汇整在一起, 如果一支内存可达 64 位,两支内存就可以达到 128 位了,这就是双通道的设计理念.
  * **BIOS**: BIOS(Basic Input Output System)是一套程序,这套程序是写死到主板上面的一个内存芯片中, 这个内存芯片在没有通电时也能够将数据记录下来,那就是只读存储器(Read Only Memory, ROM)，通常使用CMOS.BIOS是一种韧体(韧体就是写入到硬件中的软件程序).BIOS是开机时计算机系统会主动执行的第一个程序.
  * **显存、GPU**: 显示适配器又称为 VGA(Video Graphics Array),他对亍图形影像的显示扮演关键的角色.一般对于图形影像的显示重点在y于分辨率与颜色深度,因为每个图像显示的颜色会占用掉内存，所以具有了"显存"，由于3D动画等对运算能力的要求，就出现了"GPU".
  * **磁盘缓冲存储器**：主要可以将硬盘内常使用的数据快取起来,以加速系统的读取效能，目前主流的产品可达 16MB 左史的内存大小.
  * 主板功能  
    * 芯片组功能  
    * 设备I/O地址和IRQ中断信道  
        每个计算机组件都有唯一的一个I/O地址，各装置可以透过 IRQ 中断信道来告知 CPU 该装置的工作情况.
    * CMOS和BIOS  
       CMOS 主要癿功能为记录主板上面的重要参数, 包括系统时间、CPU 电压与频率、各项设备的 I/O 地址与 IRQ 等,由于这些数据的记录要花费电力,因此主板上面才有电池。BIOS 为写入到主板上某一块 flash 或 EEPROM癿程序,他可以在开机的时候执行,以加载CMOS 当中的参数, 并尝试调用存储设备中的开机程序,进进入操作统当中.  
    * 连接设备
    比如PS/2(过时的键盘鼠标接口), USB，声音输入输入与麦克风，RU-45接口等
* 文字编码系统
  * 这篇文章讲的特别好和全面<font color="blue">https://www.zhihu.com/question/23374078</font>
* 操作系统
  * 内核程序所防止到内存当中的区块是受保护的，并且开机后就一直常驻在内存当中.
  * 计算机系统主要由硬件构成，内核程序主要用于管理硬件，提供合理的计算机系统资源分配，包括CPU资源和内存使用等，因此<b>只要硬件不同，内核就要进行修改才行</b>.(可以想象arm架构几乎不可能安装windows)![1](https://cloud.githubusercontent.com/assets/16068384/20914907/9d93edb6-bbbd-11e6-804f-fc4124d5ba86.png)
  * 通常操作系统除了内核程序以外，还会提供一整组开发接口，那就是系统调用层.
  * 内核至少功能
    * 系统调用接口
    * 进程管理
    * 内存管理
    * 文件系统管理
    * 设备驱动接口(驱动程序是由厂商提供的，操作系统只需要提供开发接口)

## Unit 1 Linux是什么
* Linux是什么
  * 早起的Linux是针对386来开发的
  * 纯种的Unix: System V和BSD
  * **GNU**: (GNU's Not Unix)目的是创建一个自由、开放的UNIX操作系统
  * **FSF**: 自由软件基金会，目的是更多的工程师与志愿者来开发UNIX软件
  * **GPL**: 通用公共许可证, 目的是避免GNU所开发的自由软件被其他人所利用成为专利软件
  * **Free Software**: 自由并不是指价格的免费，而是指对软件的自由度，使用者可以自由癿执行、复制、再发行、学习、修改与强化自由软件
* Tirvakds的Linux开发
  * POSIX(Portable Operating System Interface),重点在于规范内核与应用程序之间的接口，由IEEE发布
  * 一张图看懂主流开源协议，传送门<font color="blue">http://www.ruanyifeng.com/blog/2011/05/how_to_choose_free_software_licenses.html</font>

## Unit 3 主机规划与磁盘分区
* Linux与硬件的搭配
  * 在linux系统中，几乎所有的设备文件都在/dev这个目录内.
  * 各硬件设备在linux中的文件名![1](https://cloud.githubusercontent.com/assets/16068384/20918313/5a8cb174-bbd1-11e6-831d-47a56c40e604.png) 
  * IDE接口设备的命名是事先定好的，SATA接口设备要根据Linux内核检测到的顺序命名.
* 磁盘分区
  * 磁盘的第一个扇区记录了两个重要的信息:
    * 主引导分区(Master Boot Record, MBR) 可以安装引导加载程序的地方，有446bytes.
    * 分区表(partition table): 记录整块磁盘分区的状态，64bytes.
  * 磁盘分区表
    * 在磁盘分区表的64bytes区域中,又分为四组记录区（对应磁盘的四个主分区或逻辑分区P + E <= 4），每组记录区记录了该区段的起始与结束的柱面号,因此柱面是分区的最小单位.四个分区的文件名为/dev/hda1, /dev/hda2, /dev/hda3, /dev/hda4
    * 为了实现更多的分区(多于4个)，需要使用扩展分区，<b>扩展分区的目的是使用额外的扇区来记录分区信息，扩展分区无法被格式化.</b>有扩展分区分出来的区称为逻辑分区.逻辑分区编号从/dev/hd5开始.
    * 扩展分区只能有一个(操作系统的限制)
  * 开机流程
    * BIOS: 开机主动执行的韧体，会认识第一个可开机的设备.
    * MBR: 第一个可开机设备的第一个扇区内的主引导分区块，内包含引导加载程序.
    * 引导加载程序(Boot Loader): 一支可读取内核文件来执行的软件,例如grub.
    * 内核文件: 开始操作系统的功能.
  * Boot Loader是操作系统安装在MBR上的一套软件.主要功能:
    * 提供开机选项菜单
    * 载入内核文件
    * 转交其他loader,loader只会认识自己的系统分区内的可开机内核文件和其他分区的loader
  * 为什么通常在装双系统时，先win后ub则引导加载存在，而先ub后win就直接进入windows?
    * 原因是windows会自动覆盖掉MBR，以及自己所在分区的启动扇区(loader)，而linux可以手动设置和添加启动选项.
* Linux企业级用途
  * NAT(Network Address Translation)
    * 当在专用网内部的一些主机本来已经分配到了本地IP地址（即仅在本专用网内使用的专用地址），但现在又想和因特网上的主机通信（并不需要加密）时，可使用NAT方法.所有使用本地地址的主机在和外界通信时，都要在NAT路由器上将其本地地址转换成全球IP地址，才能和因特网连接.
  * SAMBA
    * 是实现一种在局域网上共享文件和打印机的一种通信协议的免费软件，相当于windows网上邻居的增强版
  * Mail(邮箱服务器)
  * Web(WWW服务器)
  * DHCP(Dynamic Host Configuration Protocol，动态主机配置协议)
    * 提供客户端自动获取IP的功能
  * Proxy(代理服务器)
    * <font color="blue">http://www.open-open.com/lib/view/open1438605096114.html</font>自己尝试在腾讯云服务器上搭建了一个VPN服务器，安卓可以连接.
  * FTP

## Unit 5 首次登录与在线求助man page
* 命令行模式
  * 命令行模式又称为终端界面(termial)
  * Linux默认提供6个terminal(tty1-tty6)让用户登录，如果安装了x window，则增加一个tty7图形用户界面,通过`ctrl + alt + Fx`进行切换.
  * 运行等级: Linux默认提供7个run level，tty1-tty6运行等级为3, tty7运行等级是5.
  * `cat /etc/issue` 查看当前发行版版本
* 在命令行模式下执行命令
  * date
  * cal
  * bc
  * ctrl+d === exit
* Linux系统的在线求助man page和info page
  * man cmd
    ![1](https://cloud.githubusercontent.com/assets/16068384/20932648/51dea944-bc10-11e6-9dc5-3533910628ab.png)
  可以通过`man {1-9} man`获得更详细的说明
  * man page内容包括以下部分
    ![1](https://cloud.githubusercontent.com/assets/16068384/20932783/cb1b88e0-bc10-11e6-9588-fced1e2291b9.png)
  * 通常mann page中的数据保存在/usr/share/man下
  * man -f cmd 和 man -k cmd
    * -f 根据关键字在联机帮助中搜索完全匹配的条目
    * -k 根据关键字搜索联机帮助
  * info命令的作用和用法与man类似，添加了超链接功能，不过感觉比较难用，原因是man用法和vim相似，而info完全不同，数据保存在/usr/share/info下.
* 其他文档
  * 一些帮助文件放在/usr/share/doc下
* “超简单”的文本编辑器nano
* 关机相关命令
  * who(查看在线)/netstat -a(查看网络的联机状态)/ps -aux(查看后台执行程序)
  * sync
  * shut [-t 秒] [参数] 时间 [警告信息]
  * reboot/halt/poweroff
  * init (切换运行等级)
    * 补充两个运行等级run level 0(关机)、run level 6(重启)，用法例如`init 0`就进行了关机.
