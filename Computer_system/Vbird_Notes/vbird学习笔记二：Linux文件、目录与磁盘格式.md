---
title: vbird学习笔记二：Linux文件、目录与磁盘格式
date: 2017-03-02 00:01:01
categories:
- Computer_system
- Vbird_Notes
tags:
- Linux
description: 《鸟哥的Linux私房菜》学习笔记
---

> 本部分内容牵涉到大量的命令，命令详解参见另一篇文章[Linux命令进阶](http://sjming.net/?p=164).

## Unit 6 Linux的文件权限与目录配置
* 用户与用户组
    * 所有用户的账户信息记录在`/etc/passwd`, 个人密码记录在`/etc/shadow`.
    * 所有的组名记录在`/etc/group`，用户组管理密码记录在 `/ect/gshadow`.
* Linux 文件权限
    * 使用`ll`命令查看文件信息时
        * 第一列是文件的类型与权限，第一个字符表示这个文件是目录、文件或链接文件等.d表示目录，-表示文件，l表示连接文件，b表示设备文件里面的可供存储的接口设备，c表示设备文件里面的串行端口设备例如键盘鼠标.
        * 第二列表示有多少文件名连接到此节点(i-node)
    * 改变权限
        * chgrp/chown/chmod，参见<font color="blue">http://blog.csdn.net/u014451076/article/details/52965325</font>
    * 文件与目录权限的意义
        * 文件的w权限具有写入、编辑、新增、修改文件内容的权限，但是并不具有删除该文件本身的权限.
        * 目录的w权限表示具有更改目录结构列表的权限
        * 目录的x权限代表用户能否进入该目录成为工作目录,cd命令实质是change directory.这也是为什么赋予权限时通常使用755.
    * 文件类型
        * 普通文件-，分为纯文本文件(ASCII)、二进制文件(binary)、数据格式文件(data,通常需要用last打开).
        * 目录d
        * 连接文件l
        * 设备与设备文件，分为块设备文件(block)、字符(character).
        * 套接字(sockets)s，通常在`/var/run`这个目录中可以看到这种文件类型.
        * 管道(FIFO,pipo)p
* Linux目录配置
    * **FHS标准(Filesystem Hierarchy Stanard)**: 目的是希望让用户可以了解到已安装软件通常放置于哪个目录下.
    * 根目录与开机有关，开机过程中只有根目录会被挂载，其他分区则在开机完成之后才会持续进行挂载.
    * `uname -r`　查看内核版本
    * `lsb_release -a`　查看distribution版本信息

## Unit 8 Linux磁盘与文件系统管理
* EXT2文件系统
    * 磁盘分区复习，原文参考[Linux规则与安装](http://sjming.net/?p=161)
        * 扇区是最小的物理存储单位，每个扇区为512B；扇区组成柱面，柱面是分区的最小单位.
        * 第一个扇区十分重要，包括了硬盘主引导记录MBR和分区表.
        * 磁盘分区意即指定分区的起始位置与结束位置
        * 在Linux系统中，IDE硬盘最多有59个逻辑分区，SATA硬盘最多有11个逻辑分区.
    * 文件系统
        * **物理块**大小是最接近扇区的整数倍大小，在EXT2中，物理块的大小是可变化的.Ext2的物理块占一个或几个连续的扇区.物理块是数据在磁盘上的存取单位，也就是每进行一次I/O操作，最小传输的数据大小.
        * **逻辑块**是文件系统操作的基本单位.逻辑块是从0开始连续编号的，逻辑块与物理块相对.
        * 文件系统通常把文件的权限和属性放置到inode中，而把实际数据放置到datablock中.
        * 每个inode和block都有编号
            * super block: 记录此文件系统的整体信息，包括inode/block的总量、使用量、剩余量，以及文件系统的格式和相关信息等.
            * inode: 记录文件的属性，一个文件占用一个inode，同时记录此文件的数据所在的block号码.
            * block: 实际记录文件的内容，一个文件可能占用多个block.
        * 在inode中记录block号码的文件系统，由于可以通过索引一次性找到所有的block，通常称为**索引式文件系统**；但是例如U盘采用FAT文件系统，没有inode的存在，只能通过链表指针的形式进行读取.
    * EXT2文件系统
        * 文件系统一开始就将inode与block规划好了，除非重新格式化，否则inode与block固定后就不在变动.但是所有的inode和block放在一起管理数量太大，EXT2文件系统就划分出了<b>块组(block group)</b>进行管理.
        * 文件系统最前面有一个启动扇区(boot sector)，这个启动扇区可以安装引导装载程序.这正迎合了之前一篇文章中所说的，在每个分区中都可以有自己的引导加载程序，不过在现在的计算机系统中，一个分区不完全等价于一个文件系统，每个文件系统中都可以有自己的引导加载程序.
        * EXT2文件系统示意图 
         ![1](https://cloud.githubusercontent.com/assets/16068384/20964394/4ee17d74-bcac-11e6-9214-4708c47e774c.png)
            * 除第一个blockgroup外，其他blockgroup如果含有superblock，也只是第一个的备份.superblcok中还有一个validbit数值，用于记录该文件系统是(0)否(1)被挂载.
            * inode节点通过多级级联方式，12个直接、1个间接、1个二间接、1个三间接来记录block的索引，能够容纳的最大的文件为16GB.
            * EXT2文件系统支持的block大小有1KB,2KB,4KB三种.
            * 文件系统描述: 描述每个blockgroup的开始和结束的block号，以及说明每个区段(superblock,bitmap,inodemap,datablock)分别介于哪些block号之间.
            * 块对照表(block bitmap): 维护未用的块(回忆之前学过的成组链接法)
            * inode对照表(inode bitmap)
            * 通常称superblock、*map为元数据，inodetable和datablock为数据存放区.
        * 做个实践，使用dumpe2fs查看superblock的信息`sudo dumpe2fs -h /dev/sda5`
    * 日志文件系统
        * 如果文件系统在运行的过程中，发生故障意外关闭等，导致实际属于与元数据不一致，则需要在开机时通过元数据和实际数据比对搜索整个文件系统进行修复.修复时间会很长.为了避免上述情况，产生了日志文件系统.
        * 日志文件系统会在文件系统中规划处一个块，专门记录写入或修订文件时的步骤.文件写入步骤变为：
            * 预备: 当系统要写入一个文件时，先在日志文件记录块中记录某个文件准备要写入的信息.
            * 实际写入: 开始写入文件的权限与数据，开始更新metadata的数据.
            * 结束: 完成数据与metadata的更新后，在日志记录块单重完成该文件的记录.
        * 在dumpe2fs的输出信息中，Journal部分就是日志部分的信息
        * EXT2是标准文件系统，EXT3增加了日志功能.
    * VFS(虚拟文件系统)
        * Linux系统通过VFS这个内核功能去管理文件系统.  
        ![1](https://cloud.githubusercontent.com/assets/16068384/20966421/26a35e32-bcb5-11e6-936d-df4bc3d523eb.png) 
* 内存交换空间(swap)
	* CPU所读取的数据都来自内存，当内存不足的时候，为了后续程序的顺利运行，内存中暂时不使用的程序与数据就会被挪到SWAP中.
	* 使用物理分区/文件构建swap


