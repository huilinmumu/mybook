## 实验0 环境搭建

### 1.前言和国内外现状

#### 前言

对操作系统课程教学的理解 ( Why )

1. 计算机科学与计算机工程相结合
   原理和实验教学内容并行进行
   	->...实验-->原理-->实验-->原理-->...
2. 强调动手编程实践
3. 实验需求：
   	理解系统 循序渐进 阅读代码
   	把握全局 功能完善 改进创新

#### 国内外现状

**国外**

![image-20220430105039149](C:\Users\AAA\AppData\Roaming\Typora\typora-user-images\image-20220430105039149.png)

**国内**

![image-20220430105112036](C:\Users\AAA\AppData\Roaming\Typora\typora-user-images\image-20220430105112036.png)

### 2.OS实验目标

#### **目标**

在OS原理和实现中建立一个桥梁

1. 对原理知识的补充和完善
   讲课内容和实验内容同步
2. 让学生对操作系统设计有一个全局的理解
   操作系统要小巧且覆盖面全
3. 适合不同层次学生的需求
   存在高中低三类学生

#### **设计思路**

1. 方便且利用理解细节
   		●大量采用开源软件
      		●实验环境: Windows/Linux
      		●源码阅读工具: understand
      		●源码文档自动生成工具: Doxygen
      		●编译环境: gcc , make，Binutils
      		●真实/虚拟运行环境: X86机器或QEMU
      		●调试工具:改进的QEMU + (GDB O.R. IDE)
      		●IDE工具: Eclipse-CDT

2. 采用小巧全面的操作系统ucore并进行改进，需要
   覆盖操作系统的关键点，为此增加:
   		●外设: I/O管理/中断管理
   		●内存:虚存管理/页表/缺页处理/页替换算法
   		●CPU :进程管理/调度器算法
           ●并发:信号量实现和同步互斥应用
           ●存储:基于链表/FAT的文件系统
3. 完整代码量控制在10000行以内
4. 提供实验讲义和源码分析文档

### 3.8个OS实验概述

#### 实验内容

1. OS启动、中断与设备管理：0200~1800行
2. 物理内存管理：1800~2500行
3. 虚拟内存管理：2500~3200行
4. 内核线程管理：3200~3600行
5. 用户进程管理：3600~4300行
6. 处理器调度：4300~5100行
7. 同步互斥：5100~6400行
8. 文件系统：6400~9999行

![image-20220430110736578](C:\Users\AAA\AppData\Roaming\Typora\typora-user-images\image-20220430110736578.png)

![image-20220430110832473](C:\Users\AAA\AppData\Roaming\Typora\typora-user-images\image-20220430110832473.png)

#### Lab1: Bootloader/Interrupt/Device Driver

启动操作系统的bootloader，了解操作系统启动前的状态和要做的准备工作，了解运行操作系统的硬件支持，操作系统如何加载到内存中，理解两类中断--“外设中断”，"陷阱中断"等;
	●基于分段机制的存储管理
	●设备管理的基本概念
	●PC启动bootloader的过程
	●bootloader的文件组成
	●编译运行bootloader的过程
	●调试bootloader的方法
	●在汇编级了解栈的结构和处理过程
	●中断处理机制
	●通过串口/并口/CGA输出字符的方法

```
proj1/
|-- boot
| |-- asm.h
| |-- bootasm.S
| ' -- bootmain.c
|-- libs
| |-- types.h
| '-- x86.h 
|-- Makefile
'-- tools
  |-- function.mk
  '-- sign.c
3 directories, 8 files
```

#### Lab2 未完待续.....

### 4.实验环境搭建

**安装实验环境**
在虚拟机上使用安装好的ubuntu实验环境
**使用实验工具**
shell命令 : Is、cd、rm、pwd...
系统维护工具: apt, git
源码阅读与编辑工具: eclipse-CDT 、understand、 gedit、 vim
源码比较工具: diff、meld
开发编译调试工具: gcc、gdb、make
硬件模拟器: qemu
**了解x86-32硬件**
 Intel 80386运行模式概述
 Intel 80386内存架构概述
 Intel 80386寄存器概述
**了解ucore编程方法和通用数据结构**
面向对象编程方法
通用数据结构

### 5.x86-32硬件介绍

#### 运行模式

80386有四种运行模式：

**实模式**：没有保护机制，16位寻址空间

>  80386加电启动后处于实模式运行状态，在这种状态下软件可访问的物理内存空间不能超过1MB，且无法发挥Intel 80386以上级别的32位CPU的4GB内存管理能力。

**保护模式**：强大的保护机制，32位寻址空间

> 支持内存分页机制，提供了对虚拟内存的良好支持。保护模式1 F80386支持多任务，还支持优先级机制，不同的程序可以运行在不同的优先级上。优先级一共分0~ 34个级别,操作系统运行在最高的优先级0上，应用程序则运行在比较低的级别上;配合良好的检查机制后，既可以在任务间实现数据的安全共享也可以很好地隔离各个任务。

SMM模式、虚拟8086模式

#### 内存架构

- 地址是访问内存空间的索引

- 物理内存地址空间是处理器提交到总线上用于访问计算机系统中的内存和外设的最终地址。一个计算机系统中只有一个物理地址空间。

- 线性地址空间是在操作系统的虚存管理之下每个运行的应用程序能访问的地址空间。每个运行的应用程序都认为自己独享整个计算机系统的地址空间，这样可让多个运行的应用程序之间相互隔离。

- 逻辑地址空间是应用程序直接使用的地址空间。

  段机制启动、页机制未启动：逻辑地址- >段机制处理>线性地址=物理地址

  段机制和页机制都启动：逻辑地址->段机制处理>线性地址>页机制处理- >物理地址

#### 寄存器

80386的寄存器可以分为8组：

- ​	通用寄存器
- ​	段寄存器
- ​	指令指针寄存器
- ​	标志寄存器
- ​	控制寄存器
- ​	系统地址寄存器,调试寄存器,测试寄存器

通用寄存器

> EAX:累加器
> EBX :基址寄存器
> ECX:计数器
> EDX :数据寄存器
> ESI :源地址指针寄存器
> EDI :目的地址指针寄存器
> EBP :基址指针寄存器
> ESP :堆栈指针寄存器

段寄存器：用来寻址

> CS :代码段(Code Segment)
> DS :数据段(Data Segment)
> ES :附加数据段(Extra Segment)
> SS :堆栈段(Stack Segment)
> FS:附加段
> GS:附加段

指令寄存器和标志寄存器

EIP :指令寄存器

> EIP的低16位就是8086的IP，它存储的是下一条要执行指令的内存地址,在分段地址转换中，表示指令的段内偏移地址。

EFLAGS :标志寄存器

> IF(Interrupt Flag) :中断允许标志位,由CLI , STI两条指令来控制;设置IF使CPU可识别外部(可屏蔽)中断请求。复位IF则禁止中断。IF 对不可屏蔽外部中断和故障中断的识别没有任何作用。
> 	CF,PF, ZF, ...



### 6.ucore部分编程技巧

uCore的双向链表结构定义

![image-20220430121631951](C:\Users\AAA\AppData\Roaming\Typora\typora-user-images\image-20220430121631951.png)

```C++
struct list_entry {
	struct list_entry *prev, *next;
};
typedef struct{
	list_entry_t free_list;
	unsigned int nr_free;
}free_area_t; 
struct Page {
	atomic_t ref;
	...... 
	list_entry_t page_link;
};
```

链表操作函数

```c++
list_init(list_entry_t *elm)
list_add_after和list_add_before
list_del(list_entry_t *listelm)
```

访问链表节点所在的宿主数据结构

```c++
free_area_t free_area;
list_entry_t *le = &free_area.free_list;
while((le=list_next(le))!= &free_area.free_list){
	struct Page *p = le2page(le, page_link);//通过le2page找到某一个page结构变量的头指针在什么地方
}
```

```c++
//指针le是链表节点的指针，member是它的名字pagelink
#define le2page(le, member) to_struct((le), struct Page, member)
//ptr链表节点地址le，type就是Page，member就是pagelink
#define to_struct(ptr, type, member)  \
	((type *)((char *)(ptr) - offsetof(type, member)))
#define offsetof(type, member)  \
	((size t)(&((type *)0)->member))
```

### 7.演示实验操作过程

#### 版本说明

教程是用VirtualBox Ubuntu14.04，笔者这里用VMware Ubuntu5.4.0 64 位

查看Ubuntu版本命令

```
cat /proc/version
```

![image-20220430123925129](C:\Users\AAA\AppData\Roaming\Typora\typora-user-images\image-20220430123925129.png)

#### 基本命令

ls：显示目录

> pwd：显示当前目录
>
> 新建目录：mkdir
>
> 移动目录：mv
>
> 删除：mr

#### 实验步骤

##### 1.下载项目

从github上 的https://github.com/kiukotsu/ucore下载 ucore lab实验：

```
git clone https://github.com/kiukotsu/ucore.git
```

![image-20220430131533440](https://s2.loli.net/2022/04/30/XTKrzjSfMJQYE6G.png)

旧版：https://github.com/kiukotsu/ucore

新版：https://github.com/chyyuu/os_kernel_lab

这里笔者使用旧版，与课程视频对应

##### 2.在Ubuntu安装eclipse-CDT环境

###### **下载准备**

1安装jdk，笔者安装的是[jdk-8u121-linux-x64](https://www.123pan.com/s/94VA-mCEVv)

2安装eclipse，下载地址：http://www.eclipse.org/downloads/packages/eclipse-ide-java-developers/heliossr1/

###### **安装JDK**

1 新建文件需要获取root权限

```
sudo root
```

2 在/opt下新建文件夹

```
sudo mkdir /opt/jvm
```

3 将JDK压缩包复制到jvm文件夹下

```
mv jdk-8u121-linux-x64.tar.gz /opt/jvm
```

4 解压JDK到/opt/jvm目录下

```
cd /opt/jvm #首先得先进入到/opt/jvm目录下，通过指令：
tar zxvf jdk-8u121-linux-x64.tar.gz -C /opt #解压文件
```

![image-20220430170630654](https://s2.loli.net/2022/04/30/EnSMOyhasTIu7mP.png)

5 配置JDK的环境变量

```
cd /etc/profile.d #进入目录
sudo vi /etc/profile #打开指令
```

打开后，在文件末尾加入以下语句,每条语句之间要空一行，输入i插入

```
export JAVA_HOME=/opt/jvm/jdk1.8.0_121

export JRE_HOME=${JAVA_HOME}/jre

export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib

export PATH=${JAVA_HOME}/bin:$PATH
```

输入这个内容后需要先按ESC，再按“：”，最后输入wq回车，就能够退出保存

查看JDK是否安装成功

```
source /etc/profile #先输入
java -version #后输入
```

安装成功后会显示jdk版本，例如我的是：

![image-20220430171227301](https://s2.loli.net/2022/04/30/UwAiOKPN9y1pjZM.png)

###### **安装eclipse**

1将eclipse压缩包解压到/opt文件夹下

```
sudo tar zxvf eclipse-inst-linux64.tar.gz -C /opt/
```

2创建eclipse桌面快捷图标

```
 cd /usr/share/applications #首先输入指令
 sudo vim eclipse.desktop #然后输入指令
```

最后将下面的代码复制到文件中

```
[Desktop Entry]
Encoding=UTF-8
Name=Eclipse
Comment=Eclipse
Exec=/opt/eclipse/eclipse
Icon=/opt/eclipse/icon.xpm
Terminal=false
StartupNotify=true
Type=Application
Categories=Application;Development;
```

其中“Exec=”后面为eclipse安装目录下的eclipse程序的位置路径，“Icon=”后面为eclipse安装目录下的图标图片的路径。读者要按照自己的路径。

3将eclipse变为可执行文件

```
指令为：sudo chmod u+x eclipse.desktop
```

4 在/usr/share/applications目录下将Eclipse图标复制到桌面

![image-20220430171749182](https://s2.loli.net/2022/04/30/jZITfJ7KROsqXnu.png)

5点击图标下载相关的eclipse版本

6笔者点击后，这里出现了如下错误（没有的可以忽略这一条）

No Java virtual machine was found after searching the following locations

翻译过来就是：在以下的地址找不到java虚拟机

解决方法：把jdk的jre文件夹copy到eclipse的目录下

![image-20220430172047879](https://s2.loli.net/2022/04/30/jIsygOzw4acJLBu.png)

最后重新点击，成功启动！

##### 3.运行ucore项目

###### 1 将代码导入

![image-20220430181255281](https://s2.loli.net/2022/04/30/t8Rr3JXaywCABv2.png)

###### **2 开始配置qemu**

![image-20220430225551977](https://s2.loli.net/2022/04/30/jrSq1GYtUs6NpK2.png)

![image-20220430225636315](https://s2.loli.net/2022/04/30/CGnY9EjmNLdOHv3.png)

```
/usr/bin/make
/home/myliuxb/ucore/labcodes_answer/lab1_result
gdb
```

###### **3 配置debug工具**

![image-20220430225842797](https://s2.loli.net/2022/04/30/6YeSQMy8qL7dxP1.png)

这里使用了Zylin，安装步骤在下面

![image-20220430225920419](https://s2.loli.net/2022/04/30/S4UaOtjVCDWXBdQ.png)

![image-20220430225938074](https://s2.loli.net/2022/04/30/98UNHSZ61XrYxuO.png)

![image-20220430230002635](https://s2.loli.net/2022/04/30/yMoJwQusrPUklTE.png)

```
target remote:1234
file /home/myliuxb/ucore/labcodes_answer/lab1_result/obj/bootblock.o
break bootmain
```

###### 4 安装zylin 

![image-20220430230113772](https://s2.loli.net/2022/04/30/FQAkK7q6wdeMhsu.png)

![image-20220430230144946](https://s2.loli.net/2022/04/30/SWgn7KJQPMB49aG.png)

```
zylin - http://opensource.zylin.com/zylincdt
```

![image-20220430182910403](https://s2.loli.net/2022/04/30/tqM7f6K8gGopUS3.png)

###### 5 编译项目

项目文件名右键：

![image-20220430230317949](https://s2.loli.net/2022/04/30/OmMuLkvbGqQWeZy.png)

###### 6 运行

![image-20220430230400358](https://s2.loli.net/2022/04/30/fHRiYCqZVPTG3Ox.png)

运行成功：

![image-20220430230620347](https://s2.loli.net/2022/04/30/bM4ulfGNwnvzkEd.png)

###### 7 gcc版本太高的错误：

可能会出现文件大于600的错误（如果没有错误请忽略这条），原因是gcc版本太高，用gcc-4.7的就可以了。不用卸载原来的，只需要更换优先级。

先看看我们系统用的[gcc](https://so.csdn.net/so/search?q=gcc&spm=1001.2101.3001.7020)和g++是什么版本：gcc -v

可能会出现安装失败（E: 软件包gcc还没有可供安装的候选者）：

1.使用如下命令修改源

sudo gedit /etc/apt/sources.list

2.在打开的文件中添加如下源（在最后面加上这两行即可）

deb http://dk.archive.ubuntu.com/ubuntu/ xenial main
deb http://dk.archive.ubuntu.com/ubuntu/ xenial universe

3.使用如下命令更新源

sudo apt update

4.使用命令安装g++4.9

sudo apt install g++-4.9

5.GCC 优先级更换

`sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.9 1`

##### 4.安装understand失败，感觉有点麻烦

版本不对，这个也还没解决

![image-20220430180424436](https://s2.loli.net/2022/04/30/HrwSIdbckhPsJpv.png)









## 实验1 ：bootload启动ucore os

### 1.0**实验内容**:

lab1中包含一个bootloader和一个OS。这个bootloader可以切换到X86保护模式，能够读磁盘并加载ELF执行文件格式，并显示字符。而这lab1中的OS只是一个可以处理时钟中断和显示字符的幼儿园级别OS。bootload(引导装入)的翻译。

Lab1主要是关于操作系统如何启动，以及如何中断，函数调用栈相关的知识。

### 1.1 x86启动顺序

硬件启动后，怎么把操作系统放到内存中去运行。

#### 1 BIOS启动过程

**从BIOS到Bootload**

CPU加电后，寄存器初始值为空。通过CS（16位的代码段寄存器）和EIP（指令指针寄存器）来决定启动的第一条指令的地址。

![image-20220501193325026](https://s2.loli.net/2022/05/01/3XjDevPF8RAn5tg.png)

![image-20220501194116317](https://s2.loli.net/2022/05/01/reXVhgPB59ztZEG.png)

![image-20220501194548581](https://s2.loli.net/2022/05/01/zYjheE68iZx1KTt.png)

计算机加电后，CPU从物理地址0xFFFFFFF0（由初始化的CS：EIP确定，此时CS和IP的值分别是0xF000和0xFFF0)）开始执行。在0xFFFFFFF0这里只是存放了一条跳转指令，通过跳转指令跳到BIOS例行程序起始点。

BIOS做完计算机硬件自检和初始化后，会选择一个启动设备（例如软盘、硬盘、光盘等），并且读取该设备的第一扇区的512字节(即主引导扇区或启动扇区)到内存一个特定的地址0x7c00处，然后CPU控制权会转移到那个地址继续执行。至此BIOS的初始化工作做完了，进一步的工作交给了ucore的bootloader。

#### 2 bootloader启动过程

**从Bootload（512字节）到OS**

BIOS将通过读取硬盘主引导扇区到内存，并转跳到对应内存中的位置执行bootloader。bootloader完成的工作包括：

- 从实模式（16/20位寻址空间-1M）切换到保护模式（32位寻址空间-4G），启用分段机制。（为后续操作系统的执行做准备）
- 读磁盘中kernel代码（ELF执行文件格式的ucore操作系统到内存
- 显示字符串信息
- 把控制权交给ucore操作系统

#### 3 段机制

段机制内容：

![image-20220501200200144](https://s2.loli.net/2022/05/01/abfOYMWrmiGxFgR.png)

![image-20220501200528504](https://s2.loli.net/2022/05/01/MFdGchvoeNTC8Hr.png)

![image-20220501201113358](https://s2.loli.net/2022/05/01/GLOJDPFm9fuY5yz.png)

![image-20220501201244442](https://s2.loli.net/2022/05/01/WO4bph6Z3xwniLg.png)

总结：你需要建立好一个GDT（全局段描述符表），这里面每一项是一个段描述符，然后我还要把相应的段寄存器CS DS等设置好对应的index，是的CS，DS等这些段寄存器能够指向全局描述符表GDT对应的项。这个项我们称为段描述符，这个描述符指出映射关系，从而可以在使能了保护机制后，使段机制能够正常的工作。

#### 4 加载ELF格式的ucore OS kernel

ucore编译之后会生成ELF格式的执行程序。

![image-20220501202225560](https://s2.loli.net/2022/05/01/z8AQLBl3hgSxMFE.png)

![image-20220501202324295](https://s2.loli.net/2022/05/01/5UdFrWMYG9k2TcE.png)

总结：能识别出很重要的一些关键信息，然后把相应的代码段 数据段从我们的文件读到我们的内存中来。

### 1.2 C函数调用的实现

具体细节上怎么实现系统调用

![image-20220501210424664](https://s2.loli.net/2022/05/01/UWo9RsVb3Nt5fiC.png)

其他注意事项：

参数和函数返回值可通过寄存器或位于内存中的栈来传递

不需要保存/恢复所有寄存器

### 1.3 gcc内联汇编

gcc是编译内核的一个编译器，有很方便的内联汇编。可以在C文件中嵌入汇编代码，就不用单独写一个汇编的文件。也有相应的规则。

![image-20220501211128651](https://s2.loli.net/2022/05/01/X4wvCIBfWtnhLsA.png)

Example：

![image-20220501211244346](https://s2.loli.net/2022/05/01/3vrE25fIbKCw8qH.png)



asm代表内联汇编的关键字，然后把上一段汇编括起来，用引号表示一段字符串。%->%%

![image-20220501211503753](https://s2.loli.net/2022/05/01/GAykC824sovlH1j.png)

![image-20220501211559863](C:\Users\AAA\AppData\Roaming\Typora\typora-user-images\image-20220501211559863.png)

![image-20220501211746817](https://s2.loli.net/2022/05/01/QxdSCXT1u2zlRZh.png)

![image-20220501212007386](https://s2.loli.net/2022/05/01/bSBF6aWgA5syVJI.png)

### 1.4 x86-32下的中断处理

一旦操作系统启动后，很重要的事情就是接管中断

#### 1 x86中断源：

**中断（异步中断、外部中断）**

在操作系统中，有三种特殊的中断事件。由CPU**外部设备**引起的外部事件如I/O中断、时钟中断、控制台中断等是异步产生的（即产生的时刻不确定），与CPU的执行无关，我们称之为异步中断(asynchronous interrupt)也称外部中断,简称中断(interrupt)。

**异常（同步中断、内部中断）**

而把在CPU**执行指令期间**检测到不正常的或非法的条件(如除零错、地址访问越界)所引起的内部事件称作同步中断(synchronous interrupt)，也称内部中断，简称异常(exception)。

**软中断（陷入中断、trap）**

把在**程序中使用请求系统服务**的系统调用而引发的事件，称作陷入中断(trap interrupt)，也称软中断(soft interrupt)，系统调用(system call)简称trap。

#### 2 CPU与操作系统如何处理中断

每个中断或异常（中断向量、中断异常编号）与一个中断服务例程（Interrupt Service Routine，简称ISR）关联，其关联关系存储在中断描述符表（IDT）中。

IDT的起始地址和大小保存在 中断描述符表寄存器（IDTR）中。操作系统在IDT中设置好各种中断向量对应的中断描述符，留待CPU在产生中断后查询对应中断服务例程的起始地址。

**处理中断的过程**：

1.当CPU收到中断或者异常的事件时，它会暂停执行当前的程序或任务

2.通过查询IDT表 跳转 到负责处理这个信号的相关处理例程中

3.在完成对这个事件的处理后再跳回到刚才被打断的程序或任务中

**IDT关联的建立**



![IDT和IDTR寄存器的结构和关系图](https://s2.loli.net/2022/05/02/H8Ntqj7bJvm6grn.png)

![image-20220502102248228](https://s2.loli.net/2022/05/02/46Zxi15oSKaMlty.png)



#### 3 能够对中断向量表（中断描述符，简称IDT Interrupt Descriptor Table）进行初始化

![image-20220502102742606](https://s2.loli.net/2022/05/02/pv2OX4iqLeRhgHM.png)

 **中断产生后的堆栈栈变化**

在不同特权级它的处理方式是不一样的，特权级是段描述符设定它到底处于哪个特权级。比如CS低两位是0表示运行于内核态，低两位是3表示运行于用户态。在内核态产生的中断依然在内核态，但是在用户态产生的中断也会跳到内核态里面去，这里面就会产生特权级的变化。对于这种特权级的变与不变，中断的保存与恢复也是不一样的。

下图显示了给出相同特权级和不同特权级情况下中断产生后的堆栈栈变化示意图：

![image-20220502104842254](https://s2.loli.net/2022/05/02/P4QonAfGvVbqLS7.png)

对于同一特权级，意味着在内核态产生的中断依然在内核态。它的栈还是同一个栈，没有发生变化，知识在这个栈上面压了一些寄存器的内容，就是在被打断的那一刻寄存器的内容。第一个是Error Code，表示严重的异常莫不是每一个中断或者异常都会产生Error Code，第二个会压入EIP和CS，是当前被打断的那个地址（或者是当前被打断的下一个地址），下一个是EfLAGS是当前被打断的时候的标志性的内容。这三个是一旦产生中断的时候，硬件会压栈压进去。它压的是在同一个栈里面。

对于不同特权级，意味着中断产生的那一刻，我们的应用程序正在用户态执行，在用户态执行的时候，我们可以看到从用户态到内核态他们是用的不同的栈。ESP和SS是当时产生中断的时候在用户态里面的那个栈的地址，在执行完毕要恢复的时候也会发生变化，不会在内核态去执行。



完成中断服务例程之后，还要返回到被打断程序继续执行：这里面对于中断服务例程来说，它会通过一个iret指令来完成。对于通常的程序来说，他是通过ret和retf完成函数的返回。

对于没有改变特权级的方式我们可以看到，其实是把在同一个栈里面，把这个弹出，根据CS和EIP来跳转到被打断的那个地方继续执行，同时还要恢复他的Eflag的值，这是IRET弹出来的时候要干的事情。



但对于RET而言，它只是弹出了EIP，跳到当时调的下一条指令去执行，对于retf而言弹出CS和EIP，实行一种远程跳转的功能，从而确保被打断的用户态的程序能够正常地继续执行。（弹出的东西更多了）

![image-20220502145211025](https://s2.loli.net/2022/05/02/whuJvDatjF2AMVO.png)



#### **4 系统调用（软中断、trap陷入）**

用户程序通过系统调用访问OS的内核服务

如何实现？

需要指定中断

使用trap或者使用特殊指令（SYSENTER/SYSEXIT）

### 1.5 小结

![image-20220502145756725](https://s2.loli.net/2022/05/02/4eXSvazbqj5DxOA.png)

### 1.6 Lab1实验过程

![image-20220504201030913](https://s2.loli.net/2022/05/04/zEXrw51UgyLcaAe.png)

![image-20220504201047167](https://s2.loli.net/2022/05/04/ARvKo1bwYrBlhsZ.png)

#### 1.6.1实验目的

操作系统是一个软件，也需要通过某种机制加载并运行它。在这里我们将通过另外一个更加简单的软件-bootloader来完成这些工作。为此，我们需要完成一个能够切换到x86的保护模式并显示字符的bootloader，为启动操作系统ucore做准备。lab1提供了一个非常小的bootloader和ucore OS，整个bootloader执行代码小于512个字节，这样才能放到硬盘的主引导扇区中。通过分析和实现这个bootloader和ucore OS，读者可以了解到：

- 计算机原理
  - CPU的编址与寻址: 基于分段机制的内存管理
  - CPU的中断机制
  - 外设：串口/并口/CGA，时钟，硬盘
- Bootloader软件
  - 编译运行bootloader的过程
  - 调试bootloader的方法
  - PC启动bootloader的过程
  - ELF执行文件的格式和加载
  - 外设访问：读硬盘，在CGA上显示字符串
- ucore OS软件
  - 编译运行ucore OS的过程
  - ucore OS的启动过程
  - 调试ucore OS的方法
  - 函数调用关系：在汇编级了解函数调用栈的结构和处理过程
  - 中断管理：与软件相关的中断处理
  - 外设管理：时钟

#### 1.6.2 练习一：理解通过make生成执行文件的过程。

> 列出本实验各练习中对应的OS原理的知识点，并说明本实验中的实现部分如何对应和体现了原理中的基本概念和关键知识点。
>
> 在此练习中，大家需要通过静态分析代码来了解：
>
> 1. 操作系统镜像文件ucore.img是如何一步一步生成的？(需要比较详细地解释Makefile中每一条相关命令和命令参数的含义，以及说明命令导致的结果)
> 2. 一个被系统认为是符合规范的硬盘主引导扇区的特征是什么？
>
> 补充材料：
>
> 如何调试Makefile
>
> 当执行make时，一般只会显示输出，不会显示make到底执行了哪些命令。
>
> 如想了解make执行了哪些命令，可以执行：
>
> ```
> $ make "V="
> ```
>
> 要获取更多有关make的信息，可上网查询，并请执行
>
> ```
> $ man make
> ```

##### **一、操作步骤**

1、输入make clean，删掉上次的make命令所产生的object文件（后缀为“.o”的文件）及可执行文件

```
myliuxb@myliuxb-virtual-machine:~/ucore/labcodes_answer/lab1_result$ make clean
rm -f -r obj bin
```

2、输入make V=这实际就是设置一个标记，使得make它的执行过程能够展现出来。能够知道Bootloader和ucore是如何一步步生成的。

```
myliuxb@myliuxb-virtual-machine:~/ucore/labcodes_answer/lab1_result$ make V=
+ cc kern/init/init.c
i386-elf-gcc -Ikern/init/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/init/init.c -o obj/kern/init/init.o
+ cc kern/libs/stdio.c
i386-elf-gcc -Ikern/libs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/libs/stdio.c -o obj/kern/libs/stdio.o
+ cc kern/libs/readline.c
i386-elf-gcc -Ikern/libs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/libs/readline.c -o obj/kern/libs/readline.o
+ cc kern/debug/panic.c
i386-elf-gcc -Ikern/debug/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/debug/panic.c -o obj/kern/debug/panic.o
+ cc kern/debug/kdebug.c
i386-elf-gcc -Ikern/debug/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/debug/kdebug.c -o obj/kern/debug/kdebug.o
+ cc kern/debug/kmonitor.c
i386-elf-gcc -Ikern/debug/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/debug/kmonitor.c -o obj/kern/debug/kmonitor.o
+ cc kern/driver/clock.c
i386-elf-gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/clock.c -o obj/kern/driver/clock.o
+ cc kern/driver/console.c
i386-elf-gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/console.c -o obj/kern/driver/console.o
+ cc kern/driver/picirq.c
i386-elf-gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/picirq.c -o obj/kern/driver/picirq.o
+ cc kern/driver/intr.c
i386-elf-gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/intr.c -o obj/kern/driver/intr.o
+ cc kern/trap/trap.c
i386-elf-gcc -Ikern/trap/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/trap/trap.c -o obj/kern/trap/trap.o
+ cc kern/trap/vectors.S
i386-elf-gcc -Ikern/trap/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/trap/vectors.S -o obj/kern/trap/vectors.o
+ cc kern/trap/trapentry.S
i386-elf-gcc -Ikern/trap/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/trap/trapentry.S -o obj/kern/trap/trapentry.o
+ cc kern/mm/pmm.c
i386-elf-gcc -Ikern/mm/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/mm/pmm.c -o obj/kern/mm/pmm.o
+ cc libs/string.c
i386-elf-gcc -Ilibs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/  -c libs/string.c -o obj/libs/string.o
+ cc libs/printfmt.c
i386-elf-gcc -Ilibs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/  -c libs/printfmt.c -o obj/libs/printfmt.o
+ ld bin/kernel
i386-elf-ld -m    elf_i386 -nostdlib -T tools/kernel.ld -o bin/kernel  obj/kern/init/init.o obj/kern/libs/stdio.o obj/kern/libs/readline.o obj/kern/debug/panic.o obj/kern/debug/kdebug.o obj/kern/debug/kmonitor.o obj/kern/driver/clock.o obj/kern/driver/console.o obj/kern/driver/picirq.o obj/kern/driver/intr.o obj/kern/trap/trap.o obj/kern/trap/vectors.o obj/kern/trap/trapentry.o obj/kern/mm/pmm.o  obj/libs/string.o obj/libs/printfmt.o
+ cc boot/bootasm.S
i386-elf-gcc -Iboot/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Os -nostdinc -c boot/bootasm.S -o obj/boot/bootasm.o
+ cc boot/bootmain.c
i386-elf-gcc -Iboot/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Os -nostdinc -c boot/bootmain.c -o obj/boot/bootmain.o
+ cc tools/sign.c
gcc -Itools/ -g -Wall -O2 -c tools/sign.c -o obj/sign/tools/sign.o
gcc -g -Wall -O2 obj/sign/tools/sign.o -o bin/sign
+ ld bin/bootblock
i386-elf-ld -m    elf_i386 -nostdlib -N -e start -Ttext 0x7C00 obj/boot/bootasm.o obj/boot/bootmain.o -o obj/bootblock.o
'obj/bootblock.out' size: 500 bytes
build 512 bytes boot sector: 'bin/bootblock' success!
dd if=/dev/zero of=bin/ucore.img count=10000
记录了10000+0 的读入
记录了10000+0 的写出
5120000 bytes (5.1 MB, 4.9 MiB) copied, 0.0416907 s, 123 MB/s
dd if=bin/bootblock of=bin/ucore.img conv=notrunc
记录了1+0 的读入
记录了1+0 的写出
512 bytes copied, 0.000462614 s, 1.1 MB/s
dd if=bin/kernel of=bin/ucore.img seek=1 conv=notrunc
记录了146+1 的读入
记录了146+1 的写出
74844 bytes (75 kB, 73 KiB) copied, 0.000676749 s, 111 MB/s
```

##### **二、问题一：**操作系统镜像文件ucore.img是如何一步一步生成的？

> 问题：操作系统镜像文件ucore.img是如何一步一步生成的？(需要比较详细地解释Makefile中每一条相关命令和命令参数的含义，以及说明命令导致的结果)

**1.大概流程**

1.在下图代码中可以看到它调用了gcc，把C的源代码编译成了所谓的.o文件，是目标文件。通过查看make的执行过程，可以发现将项目中的kern和libs boot中的C文件都编译成.o文件

![image-20220502152116015](https://s2.loli.net/2022/05/02/ot5xLKUVygMvIXP.png)

![image-20220502161321737](https://s2.loli.net/2022/05/02/ZWgu3EfxJyHb8l6.png)

最后生成的.o文件全部存在obj文件夹中

2.然后通过ld会把这些目标文件转换成 像下面bootblock.out这样的可执行文件，可以理解为是bootloader一个执行程序（后文bootblock和bootloader其实是一个意思）

![image-20220502152430081](https://s2.loli.net/2022/05/02/52rdemG6VNw3Y1t.png)

3.dd命令可以把bootloader放到一个虚拟的硬盘里面去，在这里生成一个虚拟硬盘叫ucore.img 然后我们的硬件模拟器就会基于这个虚拟硬盘中的数据来执行相应的代码。把bootloader放到ucore.img的虚拟硬盘中

![image-20220502152756024](https://s2.loli.net/2022/05/02/SHbZmyUslonctgR.png)

4.我们可以看到最后生成两个软件，第一个是kernel，第二个叫Bootloader，kernel实际上是ucore的组成部分。

![image-20220502161536958](https://s2.loli.net/2022/05/02/mtfwrL3W1upUBTG.png)

![image-20220502161612181](https://s2.loli.net/2022/05/02/3MJYneBsCcyAfuU.png)

其实如何生成这两个文件是取决于Makefile文件，但是这个文件比较复杂，其实只要关注到底用了哪些.C程序来最后生成Bootloader和ucore就够了。

**2.具体分析：（这里参考另一个[博主](https://www.cnblogs.com/cyx-b/p/11750020.html)）**

需要生成ucore.img首先需要生成bootblock，而生成bootblock需要先生成bootmain.o和bootasm.o还有sign.o

```
+ ld bin/kernel
ld -m    elf_i386 -nostdlib -T tools/kernel.ld -o bin/kernel  obj/kern/init/init.o obj/kern/libs/readline.o obj/kern/libs/stdio.o obj/kern/debug/kdebug.o obj/kern/debug/kmonitor.o obj/kern/debug/panic.o obj/kern/driver/clock.o obj/kern/driver/console.o obj/kern/driver/intr.o obj/kern/driver/picirq.o obj/kern/trap/trap.o obj/kern/trap/trapentry.o obj/kern/trap/vectors.o obj/kern/mm/pmm.o  obj/libs/printfmt.o obj/libs/string.o
```

ld将.o文件整合成可执行文件kernel，而这些.o文件是Makefile文件通过命令使用gcc把有关kernel的.c文件编译生成

```
+ ld bin/bootblock
ld -m    elf_i386 -nostdlib -N -e start -Ttext 0x7C00 obj/boot/bootasm.o obj/boot/bootmain.o -o obj/bootblock.o
'obj/bootblock.out' size: 488 bytes
build 512 bytes boot sector: 'bin/bootblock' success!
```

同理ld也将.o文件整合成可执行文件bootblock，大小为488字节,但还是放入512字节扇区中，但是，而这些.o文件也是Makefile文件通过命令使用gcc把有关bootloader的.c文件编译生成

```
dd if=/dev/zero of=bin/ucore.img count=10000
10000+0 records in
10000+0 records out
5120000 bytes (5.1 MB) copied, 0.0601803 s, 85.1 MB/s
```

创建10000块扇区，每个扇区512字节，制成ucore.img虚拟磁盘

```
dd if=bin/bootblock of=bin/ucore.img conv=notrunc
1+0 records in
1+0 records out
512 bytes (512 B) copied, 0.000141238 s, 3.6 MB/s
```

将bootblock存到ucore.img虚拟磁盘的第一块

```
dd if=bin/kernel of=bin/ucore.img seek=1 conv=notrunc
146+1 records in
146+1 records out
74923 bytes (75 kB) copied, 0.00356787 s, 21.0 MB/s
```

将kernel存到ucore.img虚拟磁盘的第二块及之后几块，注意seek1，最终ucore.img虚拟磁盘制作完成

**3.分析Makefile文件**

之前有总结过关于Makefile的规则，在第三节https://www.cnblogs.com/450go/p/16019887.html

Makefile文件中，可以看到对应的生成ucore.img的过程及相应语句如下：

```
# create ucore.img
UCOREIMG	:= $(call totarget,ucore.img)

$(UCOREIMG): $(kernel) $(bootblock)
	$(V)dd if=/dev/zero of=$@ count=10000
	$(V)dd if=$(bootblock) of=$@ conv=notrunc
	$(V)dd if=$(kernel) of=$@ seek=1 conv=notrunc

$(call create_target,ucore.img)
```

逐条分析：

**`$(kernel)`**：生成kernel。需要以下两步

**1.编译kern/目录下的C程序，生成kernel需要的.o文件**

$(call add_files_cc,$(call listf_cc,$(KSRCDIR)),kernel,$(KCFLAGS))

实际执行的命令为：

```
+ cc kern/init/init.c
i386-elf-gcc -Ikern/init/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/init/init.c -o obj/kern/init/init.o
+ cc kern/libs/stdio.c
i386-elf-gcc -Ikern/libs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/libs/stdio.c -o obj/kern/libs/stdio.o
+ cc kern/libs/readline.c
i386-elf-gcc -Ikern/libs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/libs/readline.c -o obj/kern/libs/readline.o
+ cc kern/debug/panic.c
i386-elf-gcc -Ikern/debug/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/debug/panic.c -o obj/kern/debug/panic.o
+ cc kern/debug/kdebug.c
i386-elf-gcc -Ikern/debug/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/debug/kdebug.c -o obj/kern/debug/kdebug.o
+ cc kern/debug/kmonitor.c
i386-elf-gcc -Ikern/debug/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/debug/kmonitor.c -o obj/kern/debug/kmonitor.o
+ cc kern/driver/clock.c
i386-elf-gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/clock.c -o obj/kern/driver/clock.o
+ cc kern/driver/console.c
i386-elf-gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/console.c -o obj/kern/driver/console.o
+ cc kern/driver/picirq.c
i386-elf-gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/picirq.c -o obj/kern/driver/picirq.o
+ cc kern/driver/intr.c
i386-elf-gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/intr.c -o obj/kern/driver/intr.o
+ cc kern/trap/trap.c
i386-elf-gcc -Ikern/trap/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/trap/trap.c -o obj/kern/trap/trap.o
+ cc kern/trap/vectors.S
i386-elf-gcc -Ikern/trap/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/trap/vectors.S -o obj/kern/trap/vectors.o
+ cc kern/trap/trapentry.S
i386-elf-gcc -Ikern/trap/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/trap/trapentry.S -o obj/kern/trap/trapentry.o
+ cc kern/mm/pmm.c
i386-elf-gcc -Ikern/mm/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/mm/pmm.c -o obj/kern/mm/pmm.o
```

**2.链接这些.o文件，生成kernel**

```
# create kernel target
kernel = $(call totarget,kernel)

$(kernel): tools/kernel.ld

$(kernel): $(KOBJS)
	@echo + ld $@
	$(V)$(LD) $(LDFLAGS) -T tools/kernel.ld -o $@ $(KOBJS)
	@$(OBJDUMP) -S $@ > $(call asmfile,kernel)
	@$(OBJDUMP) -t $@ | $(SED) '1,/SYMBOL TABLE/d; s/ .* / /; /^$$/d' > $(call symfile,kernel)
```

执行的实际命令为：

```
+ ld bin/kernel
ld -m    elf_i386 -nostdlib -T tools/kernel.ld -o bin/kernel  obj/kern/init/init.o obj/kern/libs/readline.o obj/kern/libs/stdio.o obj/kern/debug/kdebug.o obj/kern/debug/kmonitor.o obj/kern/debug/panic.o obj/kern/driver/clock.o obj/kern/driver/console.o obj/kern/driver/intr.o obj/kern/driver/picirq.o obj/kern/trap/trap.o obj/kern/trap/trapentry.o obj/kern/trap/vectors.o obj/kern/mm/pmm.o  obj/libs/printfmt.o obj/libs/string.o
```

**`$(bootblock)`****：**生成bootblock。需要以下三步：

**1.生成bootmain.o和bootasm.o**

```
bootfiles = $(call listf_cc,boot)
$(foreach f,$(bootfiles),$(call cc_compile,$(f),$(CC),$(CFLAGS) -Os -nostdinc))
```

执行的实际命令为

```
+ cc boot/bootasm.S
gcc -Iboot/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Os -nostdinc -c boot/bootasm.S -o         obj/boot/bootasm.o
+ cc boot/bootmain.c
gcc -Iboot/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Os -nostdinc -c boot/bootmain.c -o obj/boot/bootmain.o
```

**2.编译tools/sign.c，生成sign.o**

```
# create 'sign' tools
$(call add_files_host,tools/sign.c,sign,sign)
$(call create_target_host,sign,sign)
```

执行的实际命令为：

```
+ cc tools/sign.c
gcc -Itools/ -g -Wall -O2 -c tools/sign.c -o obj/sign/tools/sign.o
gcc -g -Wall -O2 obj/sign/tools/sign.o -o bin/sign
```

**3.链接以上的.o文件**

```
bootblock = $(call totarget,bootblock)

$(bootblock): $(call toobj,$(bootfiles)) | $(call totarget,sign)
	@echo + ld $@
	$(V)$(LD) $(LDFLAGS) -N -e start -Ttext 0x7C00 $^ -o $(call toobj,bootblock)
	@$(OBJDUMP) -S $(call objfile,bootblock) > $(call asmfile,bootblock)
	@$(OBJDUMP) -t $(call objfile,bootblock) | $(SED) '1,/SYMBOL TABLE/d; s/ .* / /; /^$$/d' > $(call symfile,bootblock)
	@$(OBJCOPY) -S -O binary $(call objfile,bootblock) $(call outfile,bootblock)
	@$(call totarget,sign) $(call outfile,bootblock) $(bootblock)

$(call create_target,bootblock)
```

执行的实际命令为：

```
+ ld bin/bootblock
ld -m  elf_i386 -nostdlib -N -e start -Ttext 0x7C00 obj/boot/bootasm.o obj/boot/bootmain.o -o obj/bootblock.o
```

`$(V)dd if=/dev/zero of=$@ count=10000`:生成一个有10000个块的文件，每个块默认512字节，用0填充。

执行的实际命令为：

```
dd ``if``=/dev/zero of=bin/ucore.img count=10000
```

`$(V)dd if=$(bootblock) of=$@ conv=notrunc`:把bootblock中的内容写到第一个块。

执行的实际命令为：

```
dd ``if``=bin/bootblock of=bin/ucore.img conv=notrunc
```

`$(V)dd if=$(kernel) of=$@ seek=1 conv=notrunc`：从第二个块开始写kernel中的内容。

执行的实际命令为：

```
dd ``if``=bin/kernel of=bin/ucore.img seek=1 conv=notrunc 
```

##### 三、问题二：一个被系统认为是符合规范的硬盘主引导扇区的特征是什么？

>  引导扇区的大小为512字节，最后两个字节为标志性结束字节0x55，0xAA，做完这样的检查才能认为是符合规范的磁盘主引导扇区。

在sign.c文件中，它完成了相应的特征的标记，查看sign.c文件

```c++
#include <stdio.h>
#include <errno.h>
#include <string.h>
#include <sys/stat.h>

int main(int argc, char *argv[]) {
    struct stat st;
    if (argc != 3) {
        fprintf(stderr, "Usage: <input filename> <output filename>\n");
        return -1;
    }
    if (stat(argv[1], &st) != 0) {
        fprintf(stderr, "Error opening file '%s': %s\n", argv[1], strerror(errno));
        return -1;
    }
    printf("'%s' size: %lld bytes\n", argv[1], (long long)st.st_size);
    if (st.st_size > 510) {
        fprintf(stderr, "%lld >> 510!!\n", (long long)st.st_size);
        return -1;
    }
    char buf[512];
    memset(buf, 0, sizeof(buf));
    FILE *ifp = fopen(argv[1], "rb");
    int size = fread(buf, 1, st.st_size, ifp);
    if (size != st.st_size) {
        fprintf(stderr, "read '%s' error, size is %d.\n", argv[1], size);
        return -1;
    }
    fclose(ifp);
    buf[510] = 0x55;
    buf[511] = 0xAA;
    FILE *ofp = fopen(argv[2], "wb+");
    size = fwrite(buf, 1, 512, ofp);
    if (size != 512) {
        fprintf(stderr, "write '%s' error, size is %d.\n", argv[2], size);
        return -1;
    }
    fclose(ofp);
    printf("build 512 bytes boot sector: '%s' success!\n", argv[2]);
    return 0;
}
```

![image-20220502171535722](https://s2.loli.net/2022/05/02/qzEO1HvKh92PBpV.png)

引导扇区的大小为512字节，最后两个字节为标志性结束字节0x55，0xAA

#### 1.6.3 练习二：使用qemu执行并调试lab1中的软件。

##### 1.题目要求：

为了熟悉使用qemu和gdb进行的调试工作，我们进行如下的小练习：

1. 从CPU加电后执行的第一条指令开始，单步跟踪BIOS的执行。
2. 在初始化位置0x7c00设置实地址断点,测试断点正常。
3. 从0x7c00开始跟踪代码运行,将单步跟踪反汇编得到的代码与bootasm.S和 bootblock.asm进行比较。
4. 自己找一个bootloader或内核中的代码位置，设置断点并进行测试。

> 提示：参考附录“启动后第一条执行的指令”，可了解更详细的解释，以及如何单步调试和查看BIOS代码。
>
> 提示：查看 labcodes_answer/lab1_result/tools/lab1init 文件，用如下命令试试如何调试bootloader第一条指令：
>
> ```
>  $ cd labcodes_answer/lab1_result/
>  $ make lab1-mon
> ```

补充材料： 我们主要通过硬件模拟器qemu来进行各种实验。在实验的过程中我们可能会遇上各种各样的问题，调试是必要的。qemu支持使用gdb进行的强大而方便的调试。所以用好qemu和gdb是完成各种实验的基本要素。

默认的gdb需要进行一些额外的配置才进行qemu的调试任务。qemu和gdb之间使用网络端口1234进行通讯。在打开qemu进行模拟之后，执行gdb并输入

```
target remote localhost:1234
```

即可连接qemu，此时qemu会进入停止状态，听从gdb的命令。

另外，我们可能需要qemu在一开始便进入等待模式，则我们不再使用make qemu开始系统的运行，而使用make debug来完成这项工作。这样qemu便不会在gdb尚未连接的时候擅自运行了。

***gdb的地址断点\***

在gdb命令行中，使用b *[地址]便可以在指定内存地址设置断点，当qemu中的cpu执行到指定地址时，便会将控制权交给gdb。

***关于代码的反汇编\***

有可能gdb无法正确获取当前qemu执行的汇编指令，通过如下配置可以在每次gdb命令行前强制反汇编当前的指令，在gdb命令行或配置文件中添加：

```
define hook-stop
x/i $pc
end
```

即可

***gdb的单步命令\***

在gdb中，有next, nexti, step, stepi等指令来单步调试程序，他们功能各不相同，区别在于单步的“跨度”上。

```
next 单步到程序源代码的下一行，不进入函数。
nexti 单步一条机器指令，不进入函数。
step 单步到下一个不同的源代码行（包括进入函数）。
stepi 单步一条机器指令。
```

##### 2.预备知识：lab1init解释

实际上练习二就是把tools/gdbinit改为tools/lab1init的样子，lab1init是课程给的答案，我们需要完成相应的内容。不熟的可以先看看lab1init中有什么。lab1init是在哪里调用了呢？

首先看lab1-mon，进入Makefile查看发现：

```
lab1-mon: $(UCOREIMG)
        $(V)$(TERMINAL) -e "$(QEMU) -S -s -d in_asm -D $(BINDIR)/q.log -monitor stdio -hda $< -serial null"
        $(V)sleep 2
        $(V)$(TERMINAL) -e "gdb -q -x tools/lab1init"
```

这条命令可以看出来大致干的两件事：

​	1.让qemu把它的执行指令记录下来，放到q.log中

​	2.和GDB结合来调试正在执行的bootloader

其中"gdb -q -x tools/lab1init"是一条初始化执行指令，lab1init文件内容如下：

```
file bin/kernel
target remote :1234
set architecture i8086
b *0x7c00
continue
x /2i $pc
```

这里面都是gdb能够识别的命令。

第一行的意思是加载bin/kernel文件，这其实就是加载符号信息了，实际上是ucore的信息。

第二、三行的意思是与qemu进行连接，通过TRP进行连接。刚开始BIOS是进入8086的16位实模式方式，一直到0x7C00在BIOS这个阶段启动，最后把bootloader加载进去，把控制权交给bootloader，那么bootloader第一条指令就是在0X7C00处。

第四行的意思是在这个位置设置一个断点，break 0x7C00

第五行continue是让系统继续运行

最后一条x/2i $pc 就是显示两条pc指令

可以输入make lab1-mon，它将启动两个窗口，一个是调试窗口，另一个是qemu，但是断下来了，就是断在了0X7C00处。

![image-20220502190144675](https://s2.loli.net/2022/05/02/4Yf7AD8qhIPSaLr.png)

如果你想显示更多调试信息，可以输入x/10i $pc，可以把当前的十条指令都显示出来。

![image-20220502190223276](https://s2.loli.net/2022/05/02/rOCesuytS9UnJko.png)

这些指令在什么地方？我们启动代码boot文件夹下存放bootloader，可以看到在bootasm.S中第16行开始，它这个指令和刚才看到的gdb里面的指令是一样的

![image-20220502190627808](https://s2.loli.net/2022/05/02/IXmsgCY5L4itWUp.png)

让qemu继续运行，可以输入continue 

![image-20220502190413914](https://s2.loli.net/2022/05/02/3fZeXTiul4sbYk8.png)

看到这时候它跑的很快，就是这里面已经把ucore都加进来了

![image-20220502190340477](https://s2.loli.net/2022/05/02/HDx9hIvPuzly3kY.png)

最后可以按ctrl+C终止，输入quit退出

##### 3.问题一、从CPU加电后执行的第一条指令开始，单步跟踪BIOS的执行。

**1.修改gdbinit文件**

可以直接在eclipse里面修改也可以使用vim命令进行修改

这里我使用vim命令，注意要先进入~/ucore/labcodes_answer/lab1_result/tools目录下

输入vim gdbinit

![image-20220502191236708](https://s2.loli.net/2022/05/02/8XVbflwpnWe3sEI.png)

将gdbinit内容更改为：

```
set architecture i8086
target remote :1234
```

**2.make debug**

在Makefile中可以看到debug这个命令

```
debug: $(UCOREIMG)
	$(V)$(QEMU) -S -s -parallel stdio -hda $< -serial null &
	$(V)sleep 2
	$(V)$(TERMINAL)  -e "cgdb -q -x tools/gdbinit"
```

有两个功能

​	1.连接qemu

​	2.和CGDB结合来调试正在执行的bootloader

输入cd ..，退回到上一级目录

输入make debug，这里我出现了cgdb错误

![image-20220502193845967](https://s2.loli.net/2022/05/02/kETr7d1uKSLCVFj.png)

具体原因还没弄清，只能将Makefile文件中对应的cgdb修改为gdb,输入make debug

![image-20220502194919694](https://s2.loli.net/2022/05/02/WjpKLno37gJtmhl.png)

在gdb窗口中使用`si`命令即可单步追踪（注意：你不必每次输入si，输入一次si后，只要按回车即可执行上次的指令）

![image-20220502195022447](https://s2.loli.net/2022/05/02/3io5gz1Sn8I94VQ.png)

也可以用nexti:(nexti 单步一条机器指令，不进入函数。)

![image-20220502200128784](https://s2.loli.net/2022/05/02/iwoaKxMcTHeNOGy.png)

在gdb界面下，可通过如下命令来看BIOS的代码：x /2i $pc

![image-20220502195103193](https://s2.loli.net/2022/05/02/1dchQ6w3vp2LjAX.png)

##### 4.问题二、在初始化位置0x7c00设置实地址断点,测试断点正常。

**1.修改gdbinit文件**

```
target remote :1234     //连接qemu，此时qemu会进入停止状态，听从gdb的命令
set architecture i8086  //设置当前调试的CPU是8086
b *0x7c00   //在0x7c00处设置断点。此地址是bootloader入口点地址，可看boot/bootasm.S的start地址处
c     //continue简称，表示继续执行
x/10i $pc    //显示当前eip处的汇编指令
```

**2.make debug**　

![image-20220502195344377](https://s2.loli.net/2022/05/02/fNRcVGP43Khv58a.png)

##### 5.问题三、从0x7c00开始跟踪代码运行,将单步跟踪反汇编得到的代码与bootasm.S和 bootblock.asm进行比较。

反汇编得到的前几条代码是：

![image-20220502195441174](https://s2.loli.net/2022/05/02/zrOJZLvxHuDKBIQ.png)

下面是bootasm.S中部分代码：

```
start:
.code16                                             # Assemble for 16-bit mode
    cli                                             # Disable interrupts
    cld                                             # String operations increment

    # Set up the important data segment registers (DS, ES, SS).
    xorw %ax, %ax                                   # Segment number zero
    movw %ax, %ds                                   # -> Data Segment
    movw %ax, %es                                   # -> Extra Segment
    movw %ax, %ss                                   # -> Stack Segment

    # Enable A20:
    #  For backwards compatibility with the earliest PCs, physical
    #  address line 20 is tied low, so that addresses higher than
    #  1MB wrap around to zero by default. This code undoes this.
seta20.1:
    inb $0x64, %al                                  # Wait for not busy(8042 input buffer empty).
    testb $0x2, %al
    jnz seta20.1

    movb $0xd1, %al                                 # 0xd1 -> port 0x64
    outb %al, $0x64                                 # 0xd1 means: write data to 8042's P2 port
```

下面是bootblock.asm中的部分代码：

```
start:
.code16                                             # Assemble for 16-bit mode
    cli                                             # Disable interrupts
    7c00:	fa                   	cli    
    cld                                             # String operations increment
    7c01:	fc                   	cld    

    # Set up the important data segment registers (DS, ES, SS).
    xorw %ax, %ax                                   # Segment number zero
    7c02:	31 c0                	xor    %eax,%eax
    movw %ax, %ds                                   # -> Data Segment
    7c04:	8e d8                	mov    %eax,%ds
    movw %ax, %es                                   # -> Extra Segment
    7c06:	8e c0                	mov    %eax,%es
    movw %ax, %ss                                   # -> Stack Segment
    7c08:	8e d0                	mov    %eax,%ss
```

比较可知，三者基本一致。

##### 6.问题四、自己找一个bootloader或内核中的代码位置，设置断点并进行测试。

同上面的一样，修改**gdbinit**文件中的断点位置

![image-20220502195940989](https://s2.loli.net/2022/05/02/p1XFdgf6PZiGY3J.png)

#### 1.6.4 练习三：分析bootloader进入保护模式的过程。

##### 1.题目要求

BIOS将通过读取硬盘主引导扇区到内存，并转跳到对应内存中的位置执行bootloader。请分析bootloader是如何完成从实模式进入保护模式的。

提示：需要阅读**小节“保护模式和分段机制”**和lab1/boot/bootasm.S源码，了解如何从实模式切换到保护模式，需要了解：

- 为何开启A20，以及如何开启A20
- 如何初始化GDT表
- 如何使能和进入保护模式

##### 2.预备知识

bootloader实际上就是完成了一些最基本的功能：

把80386的保护模式打开，使得现在软件进入了32位的寻址空间，就是寻址方式发生了改变。为了能够做好这件事情，它还需要开启A20、初始化GDT表（全局描述符表）、使能和进入保护模式

执行完ljmp之后，计算机会进入32位保护模式

**（1）汇编**

Ucore中用到的是AT&T格式的汇编，在 AT&T 汇编格式中

```
%
寄存器名要加上 '%' 作为前缀；

$
用 '$' 前缀表示一个立即操作数；

.set symbol, expression
将symbol的值设为expression
```

![img](https://s2.loli.net/2022/05/02/g5JYEtlDx1G6jwZ.png)

```
 cli 屏蔽系统中断
 
.code16  由于代码段在实模式下运行，所以要告诉编译器使用16位的模式编译

标号:在x86汇编代码中，标号由唯一的名字加冒号组成。它可以出现在汇编程序的任何地方，并与紧跟其后的那行代码具有相同的地址。
概括的说 ，当程序中要跳转到另一位置时，需要有一个标识来指示新的位置，这就是标号，通过在目标地址的前面放上一个标号，可以在指令中使用标号来代替直接使用地址。
seta20.1:
	inb $0x64,%al
	testb $0x2,%al
	jnz seta20.1

其他:
目标操作数在源操作数的右边；
操作数的字长由操作符的最后一个字母决定，后缀'b'、'w'、'l'分别表示操作数为字节（byte，8 比特）、字（word，16 比特）和长字（long，32比特）；
```

**（2）bootloader的作用**

1、关闭中断

2、A20 使能

3、全局描述符表初始化

4、保护模式启动

5、设置段寄存器（长跳转更新CS，根据设置好的段选择子更新其他段寄存器）

6、设置堆栈，esp 0x700 ebp 0

7、进入bootmain后读取内核映像到内存，检查是否合法，并启动操作系统，控制权交给它

**（3） 实模式**

CPU复位（reset）或加电（power on）的时候以实模式启动，处理器以实模式工作。在实模式下，内存寻址方式和8086相同，由16位段寄存器的内容乘以16（10H）当做段基地址，加上16位偏移地址形成20位的物理地址，最大寻址空间1MB，最大分段64KB。可以使用32位指令。32位的x86 CPU用做高速的8086。在实模式下，所有的段都是可以读、写和可执行的。

实模式将整个物理内存看成分段的区域，程序代码和数据位于不同区域，操作系统和用户程序并没有区别对待，而且每一个指针都是指向实际的物理地址。这样，用户程序的一个指针如果指向了操作系统区域或其他用户程序区域，并修改了内容，那么其后果就很可能是灾难性的。通过修改A20地址线可以完成从实模式到保护模式的转换。

**（4）保护模式**

实模式下，程序地址为真实的物理地址，可以访问任意地址空间，这样不同进程可能访问到其它进程程序，造成严重错误。而保护模式下，程序地址为虚拟地址，然后由OS系统管理内存访问权限，这样每个进程只能访问分配给自己的物理内存空间，保证了程序的安全性。例如Linux系统地址访问采用分页机制，在加载程序时，由OS分配的进程可以访问的物理页空间，并设置了页目录项和页表项，才能保证程序正常运行。这样程序运行时地址间接地由OS进行管理，防止进程之间互相影响，全部由OS稳定性保证。

**（5）CR0**

CR0是控制寄存器，其中包含了6个预定义标志，0位是保护允许位PE(Protedted Enable)，用于启动保护模式。如果PE位置1，则保护模式启动，如果PE=0，则在实模式下运行。

关于CR0及其他控制寄存器的详细内容可以参考以下链接：https://blog.csdn.net/wyt4455/article/details/8691500

##### 3.实验步骤

**（一）代码分析**

1、bootasm.S的代码

```
#注释：#include <asm.h>
asm.h头文件中包含了一些宏定义，用于定义gdt，gdt是保护模式使用的全局段描述符表，其中存储着段描述符。
 3 # Start the CPU: switch to 32-bit protected mode, jump into C.
# The BIOS loads this code from the first sector of the hard disk into
# memory at physical address 0x7c00 and starts executing in real mode
# with %cs=0 %ip=7c00.
此段注释说明了要完成的目的：启动保护模式，转入C函数。
这里正好说了一下bootasm.S文件的作用。计算机加电后，由BIOS将bootasm.S生成的可执行代码从硬盘的第一个扇区复制到内存中的物理地址0x7c00处,并开始执行。
此时系统处于实模式。可用内存不多于1M。
 
.set PROT_MODE_CSEG,        0x8                     # kernel code segment selector
.set PROT_MODE_DSEG,        0x10                    # kernel data segment selector
这两个段选择子的作用其实是提供了gdt中代码段和数据段的索引
.set CR0_PE_ON,             0x1                     # protected mode enable flag
这个变量是开启A20地址线的标志，为1是开启保护模式
 
# start address should be 0:7c00, in real mode, the beginning address of the running bootloader
.globl start
start:
这两行代码相当于定义了C语言中的main函数，start就相当于main，BIOS调用程序时，从这里开始执行
.code16                                             # Assemble for 16-bit mode
因为以下代码是在实模式下执行，所以要告诉编译器使用16位模式编译。
    cli                                             # Disable interrupts
    cld                                             # String operations increment
关中断，设置字符串操作是递增方向。cld的作用是将direct flag标志位清零，这意味着自动增加源索引和目标索引的指令(如MOVS)将同时增加它们。
 
    # Set up the important data segment registers (DS, ES, SS).
    xorw %ax, %ax                                   # Segment number zero
ax寄存器就是eax寄存器的低十六位，使用xorw清零ax，效果相当于movw $0, %ax。 但是好像xorw性能好一些，google了一下没有得到好答案
    movw %ax, %ds                                   # -> Data Segment
    movw %ax, %es                                   # -> Extra Segment
    movw %ax, %ss                                   # -> Stack Segment
将段选择子清零
    # Enable A20:
    #  For backwards compatibility with the earliest PCs, physical
    #  address line 20 is tied low, so that addresses higher than
    #  1MB wrap around to zero by default. This code undoes this.
准备工作就绪，下面开始动真格的了，激活A20地址位。先翻译注释：由于需要兼容早期pc，物理地址的第20位绑定为0，所以高于1MB的地址又回到了0x00000.
好了，激活A20后，就可以访问所有4G内存了，就可以使用保护模式了。
 
怎么激活呢，由于历史原因A20地址位由键盘控制器芯片8042管理。所以要给8042发命令激活A20
8042有两个IO端口：0x60和0x64， 激活流程位： 发送0xd1命令到0x64端口 --> 发送0xdf到0x60，done！
seta20.1:
    inb $0x64, %al                                  # Wait for not busy(8042 input buffer empty).
    testb $0x2, %al
    jnz seta20.1
#发送命令之前，要等待键盘输入缓冲区为空，这通过8042的状态寄存器的第2bit来观察，而状态寄存器的值可以读0x64端口得到。
#上面的指令的意思就是，如果状态寄存器的第2位为1，就跳到seta20.1符号处执行，知道第2位为0，代表缓冲区为空
 
    movb $0xd1, %al                                 # 0xd1 -> port 0x64
    outb %al, $0x64                                 # 0xd1 means: write data to 8042's P2 port
发送0xd1到0x64端口
 
seta20.2:
    inb $0x64, %al                                  # Wait for not busy(8042 input buffer empty).
    testb $0x2, %al
    jnz seta20.2
 
    movb $0xdf, %al                                 # 0xdf -> port 0x60
    outb %al, $0x60                                 # 0xdf = 11011111, means set P2's A20 bit(the 1 bit) to 1
 
到此，A20激活完成。
    # Switch from real to protected mode, using a bootstrap GDT
    # and segment translation that makes virtual addresses
    # identical to physical addresses, so that the
    # effective memory map does not change during the switch.
转入保护模式，这里需要指定一个临时的GDT，来翻译逻辑地址。这里使用的GDT通过gdtdesc段定义。它翻译得到的物理地址和虚拟地址相同，所以转换过程中内存映射不会改变
    lgdt gdtdesc
载入gdt
    movl %cr0, %eax
    orl $CR0_PE_ON, %eax
    movl %eax, %cr0
打开保护模式标志位，相当于按下了保护模式的开关。cr0寄存器的第0位就是这个开关，通过CR0_PE_ON或cr0寄存器，将第0位置1
 
    # Jump to next instruction, but in 32-bit code segment.
    # Switches processor into 32-bit mode.
    ljmp $PROT_MODE_CSEG, $protcseg
由于上面的代码已经打开了保护模式了，所以这里要使用逻辑地址，而不是之前实模式的地址了。
这里用到了PROT_MODE_CSEG, 他的值是0x8。根据段选择子的格式定义，0x8就翻译成：
　　　　　　　　INDEX　　　　　　　　 TI     CPL
0000 0000 1      00      0
INDEX代表GDT中的索引，TI代表使用GDTR中的GDT， CPL代表处于特权级。
 
PROT_MODE_CSEG选择子选择了GDT中的第1个段描述符。这里使用的gdt就是变量gdt。下面可以看到gdt的第1个段描述符的基地址是0x0000,所以经过映射后和转换前的内存映射的物理地址一样。
.code32                                             # Assemble for 32-bit mode
protcseg:
    # Set up the protected-mode data segment registers
    movw $PROT_MODE_DSEG, %ax                       # Our data segment selector
    movw %ax, %ds                                   # -> DS: Data Segment
    movw %ax, %es                                   # -> ES: Extra Segment
    movw %ax, %fs                                   # -> FS
    movw %ax, %gs                                   # -> GS
    movw %ax, %ss                                   # -> SS: Stack Segment
 
重新初始化各个段寄存器。
    # Set up the stack pointer and call into C. The stack region is from 0--start(0x7c00)
    movl $0x0, %ebp
    movl $start, %esp
    call bootmain
栈顶设定在start处，也就是地址0x7c00处，call函数将返回地址入栈，将控制权交给bootmain
 
    # If bootmain returns (it shouldn't), loop.
spin:
    jmp spin
 
# Bootstrap GDT
.p2align 2                                          # force 4 byte alignment
gdt:
    SEG_NULLASM                                     # null seg
    SEG_ASM(STA_X|STA_R, 0x0, 0xffffffff)           # code seg for bootloader and kernel
    SEG_ASM(STA_W, 0x0, 0xffffffff)                 # data seg for bootloader and kernel
 
gdtdesc:
    .word 0x17                                      # sizeof(gdt) - 1
    .long gdt                                       # address gdt
```

2、<asm.h>的内容

```
// 用来定义段描述符的宏
#ifndef __BOOT_ASM_H__
#define __BOOT_ASM_H__
// assembler macros to create x86 segments
// 定义了一个空段描述符
#define SEG_NULLASM                                            \
        .word 0, 0;                                             \
        .byte 0, 0, 0, 0
//  以type,base,lim为参数定义一个段描述符, 其中的0xC0=(1100)2, 其
//  中的第一个1对应于段描述符中的G位,置1表示段界限以4KB为单位
//  第二个1对应于段描述符的D位,置1表示这是一个保护模式下的段描述符
//  具体的关于段描述符的格式定义在mmu.h中
// The 0xC0 means the limit is in 4096-byte units
// and (for executable segments) 32-bit mode.
#define SEG_ASM(type,base,lim)                                  \
        .word (((lim) >> 12) & 0xffff), ((base) & 0xffff);      \
        .byte (((base) >> 16) & 0xff), (0x90 | (type)),         \
                (0xC0 | (((lim) >> 28) & 0xf)), (((base) >> 24) & 0xff)
//  可执行段
#define STA_X     0x8       // Executable segment
//  非可执行段
#define STA_E     0x4       // Expand down (non-executable segments)
//  只能执行的段
#define STA_C     0x4       // Conforming code segment (executable only)
//  可写段但是不能执行的段
#define STA_W     0x2       // Writeable (non-executable segments)
//  可读可执行的段
#define STA_R     0x2       // Readable (executable segments)
//  表明描述符是否已被访问；把选择字装入段寄存器时，该位被标记为1
#define STA_A     0x1       // Accessed
```

**（二）问题解答**

**1.1为何开启A20，以及如何开启A20**

首先说明一点，这是一个历史遗留问题。

1981年8月，IBM公司最初推出的个人计算机IBM PC使用的CPU是Inter 8088.在该微机中地址线只有20根。在当时内存RAM只有几百KB或不到1MB时，20根地址线已经足够用来寻址这些 内存。其所能寻址的最高地址是0xffff，

也就是0x10ffef。对于超出0x100000（1MB）的寻址地址将默认地环绕到0xffef。当IBM公司与1985年引入AT机时，使用的是Inter 80286 CPU，具有24根地址线，最高可寻址16MB，并且有一个与8088那样实现地址寻址的环绕。

但是当时已经有一些程序是利用这种环绕机制进行工作的。为了实现完全的兼容性，IBM公司发明了使用一个开关来开启或禁止0x100000地址比特位。由于当时的8042键盘控制器上恰好有空闲的端口引脚（输出端口P2，引脚P21），

于是便使用了该引脚来作为与门控制这个地址比特位。该信号即被称为A20。如果它为零，则比特20及以上地址都被清除。从而实现了兼容性。

当A20地址线控制禁止时，程序就像运行在8086上，1MB以上的地址是不可访问的，只能访问奇数MB的不连续的地址。为了使能所有地址位的寻址能力，必须向键盘控制器8082发送一个命令，键盘控制器8042会将A20线置于高电位，使全部32条地址线可用，实现访问4GB内存。

 **1.2打开A20 Gate的具体步骤（参考bootasm.S）**

控制 A20 gate 的方法有 3 种：

　　1.804x 键盘控制器法

　　2.Fast A20 法

　　3.BIOS 中断法

ucore实验中用了第一种 804x 键盘控制器法，这也是最古老且效率最慢的一种。

由于在机器启动时，默认条件下，A20地址线是禁止的，所以操作系统必须使用适当的方法来开启它。

1. 等待8042 Input buffer为空；
2. 发送Write 8042 Output Port （P2）命令到8042 Input buffer；
3. 等待8042 Input buffer为空；
4. 将8042 Output Port（P2）得到字节的第2位置1，然后写入8042 Input buffer

打开A20 Gate的代码为：

```
seta20.1:
    inb $0x64, %al                                  # Wait for not busy(8042 input buffer empty).
    #从0x64端口读入一个字节的数据到al中
    testb $0x2, %al
    #如果上面的测试中发现al的第2位为0，就不执行该指令
    jnz seta20.1
　　 #循环检查　　　　　　　　　　　　　　　　　　　　　　　　　　　　
    movb $0xd1, %al                                 # 0xd1 -> port 0x64
    outb %al, $0x64                                 # 0xd1 means: write data to 8042's P2 port
    #将al中的数据写入到端口0x64中
seta20.2:
    inb $0x64, %al                                  # Wait for not busy(8042 input buffer empty).
    testb $0x2, %al
    jnz seta20.2
 
    movb $0xdf, %al                                 # 0xdf -> port 0x60
    outb %al, $0x60                                 # 0xdf = 11011111, means set P2's A20 bit(the 1 bit) to 1
```

第一步是向 804x 键盘控制器的 0x64 端口发送命令。这里传送的命令是 0xd1，这个命令的意思是要向键盘控制器的 P2 写入数据。这就是 seta20.1 代码段所做的工作。

第二步就是向键盘控制器的 P2 端口写数据了。写数据的方法是把数据通过键盘控制器的 0x60 端口写进去。写入的数据是 0xdf，因为 A20 gate 就包含在键盘控制器的 P2 端口中，随着 0xdf 的写入，A20 gate 就被打开了。

接下来要做的就是进入“保护模式”了。

 **2.1什么是GDT表**

GDT全称是Global Descriptor Table，中文名称叫“全局描述符表”，想要在“保护模式”下对内存进行寻址就先要有 GDT。GDT 表里的每一项叫做“段描述符”，用来记录每个内存分段的一些属性信息，每个“段描述符”占 8 字节。

在保护模式下，我们通过设置GDT将内存空间被分割为了一个又一个的段(这些段是可以重叠的)，这样我们就能实现不同的程序访问不同的内存空间。这和实模式下的寻址方式是不同的, 在实模式下我们只能使用address = segment << 4 | offset的方式进行寻址(虽然也是segment + offset的，但在实模式下我们并不会真正的进行分段)。在这种情况下，任何程序都能访问整个1MB的空间。而在保护模式下，通过分段的方式，程序并不能访问整个内存空间

 **2.2初始化GDT表**

为了使分段存储管理机制正常运行，需要建立好段描述符和段描述符表，全局描述符表是一个保存多个段描述符的“数组”，其起始地址保存在全局描述符表寄存器GDTR中。GDTR长48位，其中高32位为基地址，低16位为段界限。这里只需要载入已经静态存储在引导区的GDT表和其描述符到GDTR寄存器：

```
lgdt gdtdesc#CPU 单独为我们准备了一个寄存器叫做 GDTR 用来保存我们 GDT 在内存中的位置和我们 GDT 的长度。#GDTR 寄存器一共 48 位，其中高 32 位用来存储我们的 GDT 在内存中的位置，其余的低 16 位用来存我们的 GDT 有多少个段描述符。 #16 位最大可以表示 65536 个数，这里我们把单位换成字节，而一个段描述符是 8 字节，所以 GDT 最多可以有 8192 个段描述符。#CPU 不仅用了一个单独的寄存器 GDTR 来存储我们的 GDT，而且还专门提供了一个指令用来让我们把 GDT 的地址和长度传给 GDTR 寄存器：lgdt gdtdesc
```

gdtdesc 和 gdt 一起放在了 bootasm.S 文件的最底部

```
# 16 位的 gdt 大小sizeof(gdt) - 1# 32 位的 gdt 所在物理地址
```

48 位传给了 GDTR 寄存器，到此 GDT 就准备好了

 **3.1如何使能和进入保护模式**

 **3.1.1修改CR0寄存器的PE值**

如同 A20 gate 这个开关负责打开 1MB 以上内存寻址一样，想要进入“保护模式”我们也需要打开一个开关，这个开关叫“控制寄存器”，x86 的控制寄存器一共有 4 个分别是 CR0、CR1、CR2、CR3（这四个寄存器都是 32 位的），而控制进入“保护模式”的开关在 CR0 上。　

CR0中包含了6个预定义标志，0位是保护允许位PE(Protedted Enable)，用于启动保护模式，如果PE位置1，则保护模式启动，如果PE=0，则在实模式下运行。

CR0 上和保护模式有关的位，如图所示：

 ![img](https://img2018.cnblogs.com/i-beta/1776217/201911/1776217-20191109231611798-217548362.png)

打开保护模式的代码为：

```
movl %cr0, %eax``orl $CR0_PE_ON, %eax``movl %eax, %cr0
```

 

因为我们无法直接操作 CR0，所以我们首先要用一个通用寄存器来保存当前 CR0 寄存器的值，这里第一行就是用通用寄存器 eax 来保存 cr0 寄存器的值；

然后 CR0_PE 这个宏的定义在 mmu.h 文件中，是个数值 0x00000001，将这个数值与 eax 中的 cr0 寄存器的值做“或”运算后，就保证将 cr0 的第 0 位设置成了 1 即 PE = 1 保证打开了保护模式的开关。

而 cr0 的第 31 位 PG = 0 表示我们只使用分段式，不使用分页，这时再将新的计算后的 eax 寄存器中的值写回到 cr0 寄存器中就完成了到保护模式的切换。

 **3.1.2通过长跳转，更新CS寄存器的基地址**　

```
ljmp $PROT_MODE_CSEG, $protcseg
```

其中protcseg是一个标号（标号的用途在本文中的实验相关部分已说明）

由于已经使能了保护模式，所以这里要使用逻辑地址，而不是之前实模式的地址了
这里还要注意PROT_MODE_CSEG和PROT_MODE_DSEG，这两者分别定义为0x8和0x10，表示代码段和数据段的选择子。

**`根据段选择子的格式定义，0x8就翻译成：`**

**`　　　　　　　　INDEX　TI  CPL`**

　　　　　　　　**0000  0000  1000**

INDEX代表GDT中的索引，TI代表使用GDTR中的GDT， CPL代表处于特权级。

 

 **3.1.3设置段寄存器，并建立堆栈**

注意这里建立堆栈，ebp寄存器按理来说是栈帧的，但是这里并不需要把它设置为0x7c00，因为这里0x7c00是栈的最高地址，它上面没有有效内容，而之后因为调用，ebp会被设置为被调用的那个函数的栈的起始地址，这里就不用管它了。

 

```
1         movw $PROT_MODE_DSEG, %ax
2         movw %ax, %ds
3         movw %ax, %es
4         movw %ax, %fs
5         movw %ax, %gs
6         movw %ax, %ss
7         movl $0x0, %ebp
8         movl $start, %esp
```

 **3.1.4转到保护模式完成，进入boot主方法**

```
          call bootmain    
```

##### 4 总结

Bootload的启动过程可以概括如下：

首先，BIOS将**第一块扇区（存着bootloader）读到内存中物理地址为0x7c00的位置**，同时段寄存器CS值为0x0000，IP值为0x7c00，之后开始执行bootloader程序。**CLI屏蔽中断**（屏蔽所有的中断：为中断提供服务通常是操作系统设备驱动程序的责任，因此在bootloader的执行全过程中可以不必响应任何中断，中断屏蔽是通过写CPU提供的中断屏蔽寄存器来完成的）**；CLD使DF复位**，即DF=0，通过执行cld指令可以控制方向标志DF，决定内存地址是增大（DF=0，向高地址增加）还是减小（DF=1，向地地址减小）。设置寄存器 ax，ds，es，ss寄存器值为0；A20门被关闭，高于1MB的地址都默认回卷到0，所以要**激活A20**，给8042发命令激活A20，8042有两个IO端口：0x60和0x64， 激活流程： 发送0xd1命令到0x64端口 --> 发送0xdf到0x60，打开A20门。从**实模式转换到保护模式（**实模式将整个物理内存看成一块区域，程序代码和数据位于不同区域，操作系统和用户程序并没有区别对待，而且每一个指针都是指向实际的物理地址，地址就是IP值。这样，用户程序的一个指针如果指向了操作系统区域或其他用户程序区域，并修改了内容，那么其后果就很可能是灾难性的），所以就**初始化全局描述符表使得虚拟地址和物理地址匹配可以相互转换**；lgdt汇编指令把通过gdt处理后的（asm.h头文件中处理函数）**描述符表的起始位置和大小**存入gdtr寄存器中；将CR0的第0号位设置为1，**进入保护模式**；指令跳转由代码段跳到protcseg的起始位置。**设置保护模式下数据段寄存器**；**设置堆栈寄存器并调用bootmain函数**；



#### 1.6.5 练习四：分析bootloader加载ELF格式的OS的过程。

##### 1.题目要求

通过阅读bootmain.c，了解bootloader如何加载ELF文件。通过分析源代码和通过qemu来运行并调试bootloader&OS，

- bootloader如何读取硬盘扇区的？
- bootloader是如何加载ELF格式的OS？

提示：可阅读“硬盘访问概述”，“ELF执行文件格式概述”这两小节。

##### 2.整个流程

假定进入了保护模式之后，bootloader需要能够加载ELF文件。因为kenerl（就是ucore os）是以ELF的形式存在硬盘上的。

bootloader如何读取硬盘扇区的？就是说boot loader能够访问硬盘，bootloader把硬盘数据读取出来之后，要把其中ELF格式文件给分析出来。从而知道ucore它的代码段应该放在什么地方，应该有多大一块空间放这个代码段数据。哪一段空间是放数据段的数据，然后把它加载到内存中去，同时还知道跳转到ucore哪个位置去执行。

读取扇区是readsect函数，用到了in b,out b这种机器指令。in b,out b的实现都是内联汇编来实现的，它采取了一种IO空间的地址寻址方式，能够把外设的数据给读到内存中来，这也是x86里面的寻址方式。除了正常的memory方式之外，还有IO这一种寻址方式。

readsect函数这一块其实不用仔细去看，只需要知道bootloader从哪开始把相应的扇区给读进来，记忆它读多大，读完之后它就需要去进一步的分析。这个分析呢，需要去了解相应的ELF格式。



在bootmain函数中，有对ELF的格式判断，它怎么知道都进来这个扇区的数据是一个ELF格式的文件呢？它其实是读取了ELF的header，然后判断它的一个特殊的成员变量e_magic，看它是否等于一个特定的值，就认为确实是一个合法的ELF格式的文件。



在bootmain.c中有更详细的把ELF文件读取进来的一段判断。它怎么能够根据ELFheader和proghdr程序头来读出相应的代码段和数据段，然后加到相应的地方去。

最后一句![image-20220503112407992](https://s2.loli.net/2022/05/03/9iGDxf7dsCQ2pYR.png)

就是决定了bootloader把这个加载完之后，到底跳转到什么地方去，把控制权交给ucore去执行

##### 3.预备知识

**3.1ELF文件格式**

ELF(Executable and linking format)文件格式是Linux系统下的一种常用目标文件(object file)格式，有三种主要类型:

- 用于执行的可执行文件(executable file)，用于提供程序的进程映像，加载到内存执行。 这也是本实验的OS文件类型。
- 用于连接的可重定位文件(relocatable file)，可与其它目标文件一起创建可执行文件和共享目标文件。
- 共享目标文件(shared object file),连接器可将它与其它可重定位文件和共享目标文件连接成其它的目标文件，动态连接器又可将它与可执行文件和其它共享目标文件结合起来创建一个进程映像。

ELF文件结构：

![image-20220504175154533](https://s2.loli.net/2022/05/04/LpxsGmgDNMlvnK4.png)

首先，ELF文件格式提供了两种视图，分别是链接视图和执行视图。
 链接视图是以节（section）为单位，执行视图是以段（segment）为单位。链接视图就是在链接时用到的视图，而执行视图则是在执行时用到的视图。上图左侧的视角是从链接来看的，右侧的视角是执行来看的。可以看出，一个segment可以包含数个section。
 本文关注执行，结构体Proghdr是用于描述段 (segment) 的 program header，可有多个。

ELF header在文件开始处描述了整个文件的组织。ELF的文件头包含整个执行文件的控制结构。

两个结构体都定义在elf.h中：

```
struct elfhdr {		 //ELF文件头
  uint magic;  		// must equal ELF_MAGIC
  uchar elf[12];
  ushort type;
  ushort machine;
  uint version;
  uint entry;  		// 程序入口的虚拟地址
  uint phoff;  		// program header起始位置
  uint shoff;		//section header起始位置
  uint flags;
  ushort ehsize;	// ELF文件头本身大小
  ushort phentsize;
  ushort phnum;  	// program header个数
  ushort shentsize;
  ushort shnum;
  ushort shstrndx;
};
```

```
struct proghdr {//程序表头
  uint type;   	// 段类型
  uint offset;  // 段相对于ELF文件开头的偏移
  uint va;     	// 段的第一个字节将被放到内存中的虚拟地址
  uint pa;// 物理地址
  uint filesz;
  uint memsz;  	// 段在内存映像中占用的字节数,就是在内存中的大小
  uint flags;	// 读，写，执行权限
  uint align;
};
```

bootmain()函数的作用是加载 ELF格式的ucore操作系统，

**3.2 bootmain()函数**

```c++
#include <defs.h>
#include <x86.h>
#include <elf.h>

/* *********************************************************************
 * 这是一个非常简单的引导加载程序，它的唯一工作就是引导
 * 来自第一个IDE硬盘的ELF内核映像
 *
 * 磁盘布局
 * 这个程序(bootasm).S和bootmain.c是引导加载程序。
 * 应该存储在磁盘的第一个扇区。
 *
 *  *第二个扇区包含内核映像。
 *
 *  * 内核映像必须是ELF格式。
 *
 * 开机步骤
 *  * 当CPU启动时，它将BIOS加载到内存中并执行它
 *
 *  * BIOS初始化设备，设置中断例程，以及
 *    读取启动设备（硬盘）的第一个扇区
 *    进入内存并跳转到它。
 *
 *  * Assuming this boot loader is stored in the first sector of the
 *    hard-drive, this code takes over...
 *
 *  * 控制启动bootasm.S -- 设置保护模式,
 *    和一个堆栈，C代码然后运行，然后调用bootmain()
 *
 *  * bootmain()在这个文件中接管，读取内核并跳转到它
 * */
// 扇区(sector)大小512
unsigned int    SECTSIZE  =      512 ; 
// 将0x10000设为内核起始地址
struct elfhdr * ELFHDR    =      ((struct elfhdr *)0x10000) ;     // scratch space

/* waitdisk - wait for disk ready */
static void
waitdisk(void) {
    while ((inb(0x1F7) & 0xC0) != 0x40)
        /* do nothing */;
}

/* readsect - read a single sector at @secno into @dst */
static void
readsect(void *dst, uint32_t secno) {
    // wait for disk to be ready
    waitdisk();

    outb(0x1F2, 1);                         // count = 1
    outb(0x1F3, secno & 0xFF);
    outb(0x1F4, (secno >> 8) & 0xFF);
    outb(0x1F5, (secno >> 16) & 0xFF);
    outb(0x1F6, ((secno >> 24) & 0xF) | 0xE0);
    outb(0x1F7, 0x20);                      // cmd 0x20 - read sectors

    // wait for disk to be ready
    waitdisk();

    // read a sector
    insl(0x1F0, dst, SECTSIZE / 4);
}

/* *
 * readseg - read @count bytes at @offset from kernel into virtual address @va,
 * might copy more than asked.
 * */
//读取segment
static void
readseg(uintptr_t va, uint32_t count, uint32_t offset) {
    uintptr_t end_va = va + count;

    // round down to sector boundary
    va -= offset % SECTSIZE;

    // translate from bytes to sectors; kernel starts at sector 1
    uint32_t secno = (offset / SECTSIZE) + 1;

    // If this is too slow, we could read lots of sectors at a time.
    // We'd write more to memory than asked, but it doesn't matter --
    // we load in increasing order.
    for (; va < end_va; va += SECTSIZE, secno ++) {
        readsect((void *)va, secno);
    }
}

/* bootmain - the entry of bootloader */
void
bootmain(void) {
    // read the 1st page off disk
    // 从 0 开始读取 8*512 = 4096 byte 的内容到 ELFHDR
    readseg((uintptr_t)ELFHDR, SECTSIZE * 8, 0);

    // is this a valid ELF?
    // 通过储存在头部的e_magic判断是否是合法的ELF文件
    if (ELFHDR->e_magic != ELF_MAGIC) {
        goto bad;
    }

    struct proghdr *ph, *eph;

    // load each program segment (ignores ph flags)
     // 获得程序头表的起始位置 ph
    ph = (struct proghdr *)((uintptr_t)ELFHDR + ELFHDR->e_phoff);
    // 获取程序头表结束的位置 eph
    eph = ph + ELFHDR->e_phnum;
    
    // 按照描述表将ELF文件中数据载入内存
    for (; ph < eph; ph ++) {
        // 根据每个 program header 读取 segment
        // 从 p_offset 开始拷贝 p_memsz 个 byte 到 p_pa
        readseg(ph->p_va & 0xFFFFFF, ph->p_memsz, ph->p_offset);
    }

    // call the entry point from the ELF header
    // note: does not return
    // ELF文件0x1000位置后面的0xd1ec比特被载入内存0x00100000 
　　 // ELF文件0xf000位置后面的0x1d20比特被载入内存0x0010e000 
　　 // 根据ELF头部储存的入口信息，找到内核的入口
    ((void (*)(void))(ELFHDR->e_entry & 0xFFFFFF))();
	//跳到内核程序入口地址，将cpu控制权交给ucore内核代码
bad:
    outw(0x8A00, 0x8A00);
    outw(0x8A00, 0x8E00);

    /* do nothing */
    while (1);
}
```

bootasm.S完成了bootloader的大部分功能，包括打开A20，初始化GDT，进入保护模式，更新段寄存器的值，**建立堆栈**

接下来bootmain完成bootloader剩余的工作，就是把内核从硬盘加载到内存中来，并把控制权交给内核。

现在看不懂这个函数具体怎么实现的没关系，后面会有具体的解释。只需要知道它的功能就行。

**4. 问题解答**

**4.1问题一：bootloader如何读取硬盘扇区的？**

读硬盘扇区的代码如下:

```c
/* readsect - read a single sector at @secno into @dst */
static void
readsect(void *dst, uint32_t secno) {
    // wait for disk to be ready
    waitdisk();
	//读取扇区内容
    //outb(使用内联汇编实现),设置读取扇区的数目为1
    outb(0x1F2, 1);                         // count = 1
    outb(0x1F3, secno & 0xFF);
    outb(0x1F4, (secno >> 8) & 0xFF);
    outb(0x1F5, (secno >> 16) & 0xFF);
    outb(0x1F6, ((secno >> 24) & 0xF) | 0xE0);
    outb(0x1F7, 0x20);                      // cmd 0x20 - read sectors
	// 上面四条指令联合制定了扇区号　　
	// 在这4个字节联合构成的32位参数中　　
    // 29-31位强制设为1　　
    // 28位(=0)表示访问"Disk 0"　　
    // 0-27位是28位的偏移量
    
    // wait for disk to be ready
    waitdisk();
	//将扇区内容加载到内存中虚拟地址dst
    // read a sector
    insl(0x1F0, dst, SECTSIZE / 4);//也用内联汇编实现
}
```

就是把硬盘上的kernel，读取到内存中

从**`outb()`**可以看出这里是用LBA模式的PIO（Program IO）方式来访问硬盘的（即所有的IO操作是通过CPU访问硬盘的IO地址寄存器完成）。从**`磁盘IO地址和对应功能表`**可以看出，该函数一次只读取一个扇区。　　

| IO地址 | 功能                                                         |
| ------ | ------------------------------------------------------------ |
| 0x1f0  | 读数据，当0x1f7不为忙状态时，可以读。                        |
| 0x1f2  | 要读写的扇区数，每次读写前，你需要表明你要读写几个扇区。最小是1个扇区 |
| 0x1f3  | 如果是LBA模式，就是LBA参数的0-7位                            |
| 0x1f4  | 如果是LBA模式，就是LBA参数的8-15位                           |
| 0x1f5  | 如果是LBA模式，就是LBA参数的16-23位                          |
| 0x1f6  | 第0~3位：如果是LBA模式就是24-27位 第4位：为0主盘；为1从盘    |
| 0x1f7  | 状态和命令寄存器。操作时先给命令，再读取，如果不是忙状态就从0x1f0端口读数据 |

其中**`insl`**的实现如下：

```c
// x86.h
static inline void
insl(uint32_t port, void *addr, int cnt) {
    asm volatile (
            "cld;"
            "repne; insl;"
            : "=D" (addr), "=c" (cnt)
            : "d" (port), "0" (addr), "1" (cnt)
            : "memory", "cc");
}
```

**读取硬盘扇区的步骤：**

1. 等待硬盘空闲。waitdisk的函数实现只有一行：`while ((inb(0x1F7) & 0xC0) != 0x40)`，意思是不断查询读0x1F7寄存器的最高两位，直到最高位为0、次高位为1（这个状态应该意味着磁盘空闲）才返回。
2. 硬盘空闲后，发出读取扇区的命令。对应的命令字为0x20，放在0x1F7寄存器中；读取的扇区数为1，放在0x1F2寄存器中；读取的扇区起始编号共28位，分成4部分依次放在0x1F3~0x1F6寄存器中。
3. 发出命令后，再次等待硬盘空闲。
4. 硬盘再次空闲后，开始从0x1F0寄存器中读数据。注意insl的作用是"That function will read cnt dwords from the input port specified by port into the supplied output array addr."，是以dword即4字节为单位的，因此这里SECTIZE需要除以4.

**4.2 问题二：bootloader如何加载ELF格式的OS**

1. 从硬盘读了8个扇区数据到内存`0x10000`处，并把这里强制转换成`elfhdr`使用；
2. 校验`e_magic`字段；
3. 根据偏移量分别把程序段的数据读取到内存中。

之前已经看了readsect函数， `readsect`从设备的第secno扇区读取数据到dst位置

```c
static void readsect(void *dst, uint32_t secno)
```

readseg简单包装了readsect，可以从设备读取任意长度的内容。

```c
static void
readseg(uintptr_t va, uint32_t count, uint32_t offset) {
    uintptr_t end_va = va + count;

    // round down to sector boundary
    va -= offset % SECTSIZE;

    // translate from bytes to sectors; kernel starts at sector 1
    uint32_t secno = (offset / SECTSIZE) + 1;
		// 加1因为0扇区被引导占用
        // ELF文件从1扇区开始
  
    for (; va < end_va; va += SECTSIZE, secno ++) {
        readsect((void *)va, secno);
    }
}
```

最后是bootmain函数：

```c
/* bootmain - the entry of bootloader */
void
bootmain(void) {
    // read the 1st page off disk
    // 从 0 开始读取 8*512 = 4096 byte 的内容到 ELFHDR
    readseg((uintptr_t)ELFHDR, SECTSIZE * 8, 0);

    // is this a valid ELF?
    // 通过储存在头部的e_magic判断是否是合法的ELF文件
    if (ELFHDR->e_magic != ELF_MAGIC) {
        goto bad;
    }

    struct proghdr *ph, *eph;

    // load each program segment (ignores ph flags)
     // 获得程序头表的起始位置 ph
    ph = (struct proghdr *)((uintptr_t)ELFHDR + ELFHDR->e_phoff);
    // 获取程序头表结束的位置 eph
    eph = ph + ELFHDR->e_phnum;
    
    // 按照描述表将ELF文件中数据载入内存
    for (; ph < eph; ph ++) {
        // 根据每个 program header 读取 segment
        // 从 p_offset 开始拷贝 p_memsz 个 byte 到 p_pa
        readseg(ph->p_va & 0xFFFFFF, ph->p_memsz, ph->p_offset);
    }

    // call the entry point from the ELF header
    // note: does not return
    // ELF文件0x1000位置后面的0xd1ec比特被载入内存0x00100000 
　　 // ELF文件0xf000位置后面的0x1d20比特被载入内存0x0010e000 
　　 // 根据ELF头部储存的入口信息，找到内核的入口
    ((void (*)(void))(ELFHDR->e_entry & 0xFFFFFF))();
	//跳到内核程序入口地址，将cpu控制权交给ucore内核代码
bad:
    outw(0x8A00, 0x8A00);
    outw(0x8A00, 0x8E00);

    /* do nothing */
    while (1);
}
```



#### 1.6.6 练习五：实现函数调用堆栈跟踪函数

我们需要在lab1中完成kdebug.c中函数print_stackframe的实现，可以通过函数print_stackframe来跟踪函数调用堆栈中记录的返回地址。在如果能够正确实现此函数，可在lab1中执行 “make qemu”后，在qemu模拟器中得到类似如下的输出：

```
……
ebp:0x00007b28 eip:0x00100992 args:0x00010094 0x00010094 0x00007b58 0x00100096
    kern/debug/kdebug.c:305: print_stackframe+22
ebp:0x00007b38 eip:0x00100c79 args:0x00000000 0x00000000 0x00000000 0x00007ba8
    kern/debug/kmonitor.c:125: mon_backtrace+10
ebp:0x00007b58 eip:0x00100096 args:0x00000000 0x00007b80 0xffff0000 0x00007b84
    kern/init/init.c:48: grade_backtrace2+33
ebp:0x00007b78 eip:0x001000bf args:0x00000000 0xffff0000 0x00007ba4 0x00000029
    kern/init/init.c:53: grade_backtrace1+38
ebp:0x00007b98 eip:0x001000dd args:0x00000000 0x00100000 0xffff0000 0x0000001d
    kern/init/init.c:58: grade_backtrace0+23
ebp:0x00007bb8 eip:0x00100102 args:0x0010353c 0x00103520 0x00001308 0x00000000
    kern/init/init.c:63: grade_backtrace+34
ebp:0x00007be8 eip:0x00100059 args:0x00000000 0x00000000 0x00000000 0x00007c53
    kern/init/init.c:28: kern_init+88
ebp:0x00007bf8 eip:0x00007d73 args:0xc031fcfa 0xc08ed88e 0x64e4d08e 0xfa7502a8
<unknow>: -- 0x00007d72 –
……
```

请完成实验，看看输出是否与上述显示大致一致，并解释最后一行各个数值的含义。

提示：可阅读小节“函数堆栈”，了解编译器如何建立函数调用关系的。在完成lab1编译后，查看lab1/obj/bootblock.asm，了解bootloader源码与机器码的语句和地址等的对应关系；查看lab1/obj/kernel.asm，了解 ucore OS源码与机器码的语句和地址等的对应关系。

要求完成函数kern/debug/kdebug.c::print_stackframe的实现，提交改进后源代码包（可以编译执行） ，并在实验报告中简要说明实现过程，并写出对上述问题的回答。

补充材料：
由于显示完整的栈结构需要解析内核文件中的调试符号，较为复杂和繁琐。代码中有一些辅助函数可以使用。例如可以通过调用print_debuginfo函数完成查找对应函数名并打印至屏幕的功能。具体可以参见kdebug.c代码中的注释。

##### 2.预备知识

怎么能够分析函数的调用栈

这一段输出是正确的输出结果，可以看出来，如果你能正确的分析出这个栈帧格式，栈帧依赖关系，栈帧格建立的链表，那就可以把相应函数代码关系给展示出来。怎么去把正确的esp和ebp给读取出来？这是关键

**3.代码实现**

 1.编程前，首先了解下当前情况：在Terminal下输入`make qemu`，发现打印以下信息后就退出了：

![image-20220504201609780](C:\Users\AAA\AppData\Roaming\Typora\typora-user-images\image-20220504201609780.png)

2.分析print_stackframe的函数调用关系

```c
kern_init ->
    grade_backtrace ->
        grade_backtrace0(0, (int)kern_init, 0xffff0000) ->
                 grade_backtrace1(0, 0xffff0000) ->
                     grade_backtrace2(0, (int)&0, 0xffff0000, (int)&(0xffff0000)) ->
                        mon_backtrace(0, NULL, NULL) ->
                             print_stackframe -> 
```

3.找到print_stackframe函数，发现函数里面的注释已经提供了十分详细的步骤，基本上按照提示来做就行了。代码如下所示。

- 首先定义两个局部变量ebp、esp分别存放ebp、esp寄存器的值。这里将ebp定义为指针，是为了方便后面取ebp寄存器的值。
- 调用read_ebp函数来获取执行print_stackframe函数时ebp寄存器的值，这里read_ebp必须定义为inline函数，否则获取的是执行read_ebp函数时的ebp寄存器的值。
- 调用read_eip函数来获取当前指令的位置，也就是此时eip寄存器的值。这里read_eip必须定义为常规函数而不是inline函数，因为这样的话在调用read_eip时会把当前指令的下一条指令的地址（也就是eip寄存器的值）压栈，那么在进入read_eip函数内部后便可以从栈中获取到调用前eip寄存器的值。
- 由于变量eip存放的是下一条指令的地址，因此将变量eip的值减去1，得到的指令地址就属于当前指令的范围了。由于只要输入的地址属于当前指令的起始和结束位置之间，print_debuginfo都能搜索到当前指令，因此这里减去1即可。
- 以后变量eip的值就不能再调用read_eip来获取了（每次调用获取的值都是相同的），而应该从ebp寄存器指向栈中的位置再往上一个单位中获取。
- 由于ebp寄存器指向栈中的位置存放的是调用者的ebp寄存器的值，据此可以继续顺藤摸瓜，不断回溯，直到ebp寄存器的值变为0

 

```c
void
print_stackframe(void) {
     /* LAB1 YOUR CODE : STEP 1 */
     /* (1) call read_ebp() to get the value of ebp. the type is (uint32_t);
      * (2) call read_eip() to get the value of eip. the type is (uint32_t);
      * (3) from 0 .. STACKFRAME_DEPTH
      *    (3.1) printf value of ebp, eip
      *    (3.2) (uint32_t)calling arguments [0..4] = the contents in address (unit32_t)ebp +2 [0..4]
      *    (3.3) cprintf("\n");
      *    (3.4) call print_debuginfo(eip-1) to print the C calling function name and line number, etc.
      *    (3.5) popup a calling stackframe
      *           NOTICE: the calling funciton's return addr eip  = ss:[ebp+4]
      *                   the calling funciton's ebp = ss:[ebp]
      */

	  uint32_t *ebp = 0;
	  uint32_t esp = 0;

	  ebp = (uint32_t *)read_ebp();
	  esp = read_eip();

	   while (ebp)
	   {
	      cprintf("ebp:0x%08x eip:0x%08x args:", (uint32_t)ebp, esp);
	      cprintf("0x%08x 0x%08x 0x%08x 0x%08x\n", ebp[2], ebp[3], ebp[4], ebp[5]);

	      print_debuginfo(esp - 1);

	     esp = ebp[1];
	     ebp = (uint32_t *)*ebp;
	  }


}
```

 4.编码完成后，执行`make qemu`，打印结果与实验指导书的结果类似。



最后一行是`ebp:0x00007bf8 eip:0x00007d6e args:0xc031fcfa 0xc08ed88e 0x64e4d08e 0xfa7502a8`，共有ebp，eip和args三类参数，下面分别给出解释。

1. `ebp:0x0007bf8` 此时ebp的值是kern_init函数的栈顶地址，从obj/bootblock.asm文件中知道整个栈的栈顶地址为0x00007c00，ebp指向的栈位置存放调用者的ebp寄存器的值，ebp+4指向的栈位置存放返回地址的值，这意味着kern_init函数的调用者（也就是bootmain函数）没有传递任何输入参数给它！因为单是存放旧的ebp、返回地址已经占用8字节了。
2. `eip:0x00007d6e` eip的值是kern_init函数的返回地址，也就是bootmain函数调用kern_init对应的指令的下一条指令的地址。这与obj/bootblock.asm是相符合的。

```
    7d6c:   ff d0                   call   *%eax
    7d6e:   ba 00 8a ff ff          mov    $0xffff8a00,%edx
```

`args:0xc031fcfa 0xc08ed88e 0x64e4d08e 0xfa7502a8` 一般来说，args存放的4个dword是对应4个输入参数的值。但这里比较特殊，由于bootmain函数调用kern_init并没传递任何输入参数，并且栈顶的位置恰好在boot loader第一条指令存放的地址的上面，而args恰好是kern_int的ebp寄存器指向的栈顶往上第2~5个单元，因此args存放的就是boot loader指令的前16个字节！可以对比obj/bootblock.asm文件来验证（验证时要注意系统是小端字节序）。

```
00007c00 <start>:
    7c00:   fa                      cli    
    7c01:   fc                      cld    
    7c02:   31 c0                   xor    %eax,%eax
    7c04:   8e d8                   mov    %eax,%ds
    7c06:   8e c0                   mov    %eax,%es
    7c08:   8e d0                   mov    %eax,%ss
    7c0a:   e4 64                   in     $0x64,%al
    7c0c:   a8 02                   test   $0x2,%al
    7c0e:   75 fa                   jne    7c0a <seta20.1>
```

 

#### 1.6.7 练习六：完善中断初始化和处理

##### 1.题目要求

请完成编码工作和回答如下问题：

1. 中断描述符表（也可简称为保护模式下的中断向量表）中一个表项占多少字节？其中哪几位代表中断处理代码的入口？
2. 请编程完善kern/trap/trap.c中对中断向量表进行初始化的函数idt_init。在idt_init函数中，依次对所有中断入口进行初始化。使用mmu.h中的SETGATE宏，填充idt数组内容。每个中断的入口由tools/vectors.c生成，使用trap.c中声明的vectors数组即可。
3. 请编程完善trap.c中的中断处理函数trap，在对时钟中断进行处理的部分填写trap函数中处理时钟中断的部分，使操作系统每遇到100次时钟中断后，调用print_ticks子程序，向屏幕上打印一行文字”100 ticks”。

> 【注意】除了系统调用中断(T_SYSCALL)使用陷阱门描述符且权限为用户态权限以外，其它中断均使用特权级(DPL)为０的中断门描述符，权限为内核态权限；而ucore的应用程序处于特权级３，需要采用｀int 0x80`指令操作（这种方式称为软中断，软件中断，Tra中断，在lab5会碰到）来发出系统调用请求，并要能实现从特权级３到特权级０的转换，所以系统调用中断(T_SYSCALL)所对应的中断门描述符中的特权级（DPL）需要设置为３。

要求完成问题2和问题3 提出的相关函数实现，提交改进后的源代码包（可以编译执行），并在实验报告中简要说明实现过程，并写出对问题1的回答。完成这问题2和3要求的部分代码后，运行整个系统，可以看到大约每1秒会输出一次”100 ticks”，而按下的键也会在屏幕上显示。

##### 2.预备知识

主要是建立中断

#### 1.6.8 Lab1实验总结：

以树形结构显示文件目录：

```
tree -L
```



各个文件的作用：

```
├── bin
│   ├── bootblock
│   ├── kernel #就是ucore，负责中断初始化，产生时钟总段，能够把信息显示在屏幕上
│   ├── sign
│   └── ucore.img
├── boot
│   ├── asm.h	#包含了一些宏定义，用于定义gdt
│   ├── bootasm.S	#启动保护模式，转入C函数bootmain
│   └── bootmain.c	#完成读扇区和分析ELF文件	
├── kern
│   ├── debug
│   │   ├── assert.h
│   │   ├── kdebug.c
│   │   ├── kdebug.h
│   │   ├── kmonitor.c
│   │   ├── kmonitor.h
│   │   ├── panic.c
│   │   └── stab.h
│   ├── driver
│   │   ├── clock.c
│   │   ├── clock.h
│   │   ├── console.c
│   │   ├── console.h
│   │   ├── intr.c
│   │   ├── intr.h
│   │   ├── kbdreg.h
│   │   ├── picirq.c
│   │   └── picirq.h
│   ├── init
│   │   └── init.c
│   ├── libs
│   │   ├── readline.c
│   │   └── stdio.c
│   ├── mm
│   │   ├── memlayout.h
│   │   ├── mmu.h
│   │   ├── pmm.c
│   │   └── pmm.h
│   └── trap
│       ├── trap.c
│       ├── trapentry.S
│       ├── trap.h
│       └── vectors.S
├── libs
│   ├── defs.h
│   ├── elf.h
│   ├── error.h
│   ├── printfmt.c
│   ├── stdarg.h
│   ├── stdio.h
│   ├── string.c
│   ├── string.h
│   └── x86.h
├── Makefile
├── obj
│   ├── boot
│   │   ├── bootasm.d
│   │   ├── bootasm.o
│   │   ├── bootmain.d
│   │   └── bootmain.o
│   ├── bootblock.asm
│   ├── bootblock.o		#可执行文件
│   ├── bootblock.out	#可执行文件
│   ├── bootblock.sym
│   ├── kern
│   │   ├── debug
│   │   │   ├── kdebug.d
│   │   │   ├── kdebug.o
│   │   │   ├── kmonitor.d
│   │   │   ├── kmonitor.o
│   │   │   ├── panic.d
│   │   │   └── panic.o
│   │   ├── driver
│   │   │   ├── clock.d
│   │   │   ├── clock.o
│   │   │   ├── console.d
│   │   │   ├── console.o
│   │   │   ├── intr.d
│   │   │   ├── intr.o
│   │   │   ├── picirq.d
│   │   │   └── picirq.o
│   │   ├── init
│   │   │   ├── init.d
│   │   │   └── init.o
│   │   ├── libs
│   │   │   ├── readline.d
│   │   │   ├── readline.o
│   │   │   ├── stdio.d
│   │   │   └── stdio.o
│   │   ├── mm
│   │   │   ├── pmm.d
│   │   │   └── pmm.o
│   │   └── trap
│   │       ├── trap.d
│   │       ├── trapentry.d
│   │       ├── trapentry.o
│   │       ├── trap.o
│   │       ├── vectors.d
│   │       └── vectors.o
│   ├── kernel.asm
│   ├── kernel.sym
│   ├── libs
│   │   ├── printfmt.d
│   │   ├── printfmt.o
│   │   ├── string.d
│   │   └── string.o
│   └── sign
│       └── tools
│           ├── sign.d
│           └── sign.o
├── report.md
└── tools
    ├── function.mk
    ├── gdbinit
    ├── grade.sh
    ├── kernel.ld
    ├── lab1init
    ├── moninit
    ├── sign.c
    └── vector.c

```

boot文件





