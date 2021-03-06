> 计算机操作系统 
>
> 教材《计算机操作系统（第四版）》 汤小丹等
>
> PPT 四川大学 杜中军老师
>
> Find more on github.com/textGuan/Review

# 第五章 存储器管理

熟悉存储管理目的和功能，掌握地址重定位的概念。

熟悉单一连续分配、固定分区分配、动态分区分配实现原理；掌握可变式分区分配的数据结构和分配回收算法，掌握动态重定位分区分配实现原理和分配算法。

熟悉覆盖与交换的概念。

熟练掌握分页存储管理原理，熟练掌握基本的地址变换机构和具有快表的地址变换机构，了解两级页表机制。

掌握分段存储管理原理和分段地址变换机构，掌握分页和分段比较，熟悉分页和分段的共享，掌握段页式存储管理原理和地址变换机构。

## 5.1 存储管理概述

### 5.1.1 存储管理目的和功能

主存储器的分配和回收——内存分配的主要任务是为每一道程序分配内存空间

提高主存储器的利用率，减少不可用的存储空间，允许多道程序共享主存

存储保护——内存保护的任务是确保每道程序都在自己的内存空间运行，互不干扰

内存扩充——从逻辑上扩充内存容量，使用户认为系统所拥有的内存空间远比其实际的内存空间（RAM）大得多

地址重定位

### 5.1.2 地址重定位

**符号地址**：源程序中通过符号访问子程序和数据，这些符号名实际代表了地址

**名字空间**：程序中符号名的集合

**逻辑地址/程序地址/虚地址**：程序中使用的从0开始进行编址的地址（一维或二维）

**（逻辑）地址空间/程序空间**：程序中逻辑地址的集合

**内存地址/物理地址/绝对地址**：内存空间的地址

**物理空间/存储空间/内存空间**：内存地址的集合，一维线性空间

**地址重定位**：确定装入内存的实际物理地址，并修改程序中与地址有关的代码，把逻辑地址变换为物理地址，这个过程叫做地址重定位。

```
按照重定位的时机分为静态重定位和动态重定位
```

**静态重定位**：在程序执行之前进行重定位，根据装配模块将要装入的内存起始地址，直接修改装配模块中的有关使用地址的指令，无需硬件支持

```
程序重定位之后就不能在内存中移动
要求程序的存储空间是连续的，不能把程序存储到若干个不连续的区域中
```

​       **动态重定位**：在程序执行过程中进行地址重定位，即在每次访问内存单元前才进行内存变换，需要硬件——重定位寄存器的支持

```
目标程序在内存中可以移动
部分装入内存程序即可运行
便于多个进程共享同一程序副本
```



## 5.2 程序装入和链接

### 5.2.1 程序装入

绝对装入：程序使用绝对地址编写，装入内存时装入到程序规定的地方，不需地址变换

静态重定位装入：程序使用逻辑地址编写，装入内存后在执行之前进行地址变换

动态重定位装入：程序使用逻辑地址编写，装入内存后在执行每条指令存取内存前进行地址变换

### 5.2.2 程序链接

静态链接：编译/汇编后，将目标模块在运行之前组合在一起形成一个独立运行模块

装入时动态链接：经编译后的模块在装入内存准备运行前进行链接

```
优点：便于修改和更新程序，便于共享，节省外存空间
缺点：并没有减少内存占用量
```

运行时动态链接：在执行过程中若发现需要的某目标模块没有装入内存链接，则将其装入内存并链接

```
优点：模块共享，提高内存利用率
```



## 5.3 连续分配存储方式

### 5.3.1 单一连续分配

将内存分为两个区：

系统区：仅供操作系统使用，通常设置在内存的低段

用户区：除系统区之外的全部内存空间，提供给用户使用

存储保护：设置基址界限寄存器对

```
缺点：只支持单道程序运行，不能充分利用内存空间，不能实现虚拟存储
```



### 5.3.2 固定分区分配

又称为静态分区，将内存的用户区域分成大小不等的分区，以适应不同大小的作业的需要

管理机构：系统有一张分区说明表，每个表目说明一个分区的大小，起始地址和是否已分配的使用标志。

内存管理：分配算法和回收算法

存储保护：设置基址界限寄存器对

```
优点：支持多道程序
缺点：易产生碎片，不支持虚拟存储，不能充分利用内存，当空闲分区之和能满足某程序而单个不能满足时，程序不能运行
```

 

### 5.3.3 动态/可变式分区分配

在分配时，首先找到一个足够大的空闲分区，即这个空闲区的大小比作业要求的要大，系统则将这个空闲分区分成两部分：一部分称为已分配的分区，剩余的部分仍作为空闲区。

```
管理机构：

空闲区表形式：空闲分区表为每个尚未分配的分区设置一个表项，包括分区的序号、大小、始址和状态

空闲区链形式：为了实现对空闲分区的分配和链接，在每个分区的起始部分，设置一些用于控制分区分配的信息（如分区的大小和状态位），以及用于链接其他分区的前向指针；在分区尾部，则设置了一个后向指针，为了检索方便也设置了控制分区分配的信息。然后，通过前、后向指针将所有的分区链接成一个双向链表。
```



### 5.3.4 动态重定位分区分配

与动态分区一样，但是分区可以移动以便把较小的空闲分区合并成一个较大的空闲分区，因而需要动态重定位的支持

```
优点：支持多道程序
缺点：不能解决虚拟存储，移动合并需要增加系统开销，仍然存在碎片
```

 

**分区分配算法**

最佳适应算法：从全部空闲区中找出能满足作业要求的、且大小最小的空闲分区，为适应这种算法，空闲分区表（空闲区链）中的空闲分区要按大小从小到大进行排序，自表头开始查找到第一个满足要求的自由分区分配。

首次适应算法：从空闲分区表的第一个表目起查找该表，把最先能够满足要求的空闲区分配给作业，减少查找时间，要求空闲分区表（空闲区链）中的空闲分区要按地址由低到高进行排序。

循环首次适应算法：与首次适应算法相似，从上次找到的空闲区的下一个空闲区开始查找直到找到第一个能满足要求的空闲区为止，使内存中的空闲区分布得比较均匀。

最坏适应法：从所有未分配的分区中挑选最大的且大于和等于作业大小的分区分给要求的作业；空闲分区按大小由大到小排序。

 

## 5.4 覆盖和交换

### 5.4.1 覆盖技术

一个作业的若干程序段（或数据段）间，或者几个作业的某些程序段间共享某主存空间。覆盖技术通常与单用户连续分配、固定分区和可变分区等存储管理技术配合使用。不但用于用户作业的运行中，还常用于操作系统本身的运行中

```
覆盖数据结构：
包括覆盖段号、覆盖数、当前覆盖号、覆盖区始址、覆盖区长度、起始盘区号等
```

### 5.4.2 交换（对换）技术

将内存中暂不能运行的进程调出到外存上，以腾出足够的内存空间，将已具备运行条件的进程换入内存。

当内核发现内存不足时调用对换进程，对换进程检查驻留在内存的进程，选择处于阻塞和睡眠状态的进程换出，如无则选择优先级低的驻留内存时间长的处于就绪状态的进程换出，而当内存又空时对换进程在对换区中选择换出时间长的处于就绪态的进程调入。

 

## 5.5 分页存储管理

### 5.5.1 分页存储管理原理

分页存储管理是将一个进程的地址空间划分为若干个大小相等的区域，称为页，相应的，将内存空间划分成与页相同大小的若干个物理块，称为块或页框。在为进程分配内存时，将进程中的若干页分别装入多个不相邻接的块中。

```
分页系统的地址结构：由两部分组成，前一部分为页号P，后一部分为位移量W，即页内地址。
```

在将进程的每一页离散地分配到内存的多个物理块中后，系统应能保证在内存中找到每个页面所对应的物理块。为此，系统为每个进程建立了一张页面映射表（页表），每个页在页表中占一个表项，记录该页在内存中对应的物理块号。页表的作用是实现从页号到物理块号的地址映射。

 

### 5.5.2 地址变换机构

利用页表将用户程序中的逻辑地址变换成内存中的物理地址，每个进程对应的页表的始址和页表的长度存放在进程的PCB中，当进程被调度时，就将他们装入页表寄存器。

为了提高地址变换的速度，在地址变换机构中增设了一个具有并行查询功能的特殊的高速缓冲存储器——快表（或联想存储器），用以存放当前访问的那些页表项。

```
假设检索联想存储器的时间为20ns，访问内存的时间为100ns，访问联想存储器的的命中率为85%，则CPU存取一个数据的平均时间为T=0.85*120+0.15*220=135ns，所以访问时间只增加35%。如果不引入联想存储器，其访问将延长一倍（达200ns）。
```

 

### 5.5.3 两级页表机制

将页表进行分页，每个页面的大小与内存物理块的大小相同，并为它们进行编号，可以离散地将各个页面分别存放在不同的物理块中，为此再建立一张页表，称为外层页表（页表目录），即第一级页表，其中每个表目是存放某个页表的物理地址。第二级才是页表，其中每个表目所存放的才是页的物理块号。

 

### 5.5.4 存储管理算法

数据结构

```
在PCB中记录页表的地址和长度
设置内存块表，记录每个块的状态（已分配/空闲），整个系统一张
页表：每个进程一张
```

分配和释放算法

请求分配X大小的空间，计算所需块数N（X/每页大小，向上取整），如果有N个可用块，在PCB中填写页表长度，分配页表，在PCB中填写页表地址，检查内存块表，分配N个空闲块，在每块的状态栏填写已分配，同时块号填入页表

 

### 5.5.5 评价

优点：

```
支持多道程序设计
克服可重定位动态分区要移动合并的工作，程序不必连续存放
大大减少碎片
```

缺点：

```
动态地址变换需要增加计算机的成本并降低处理机速度
各种表格要占用一定空间，需要一些系统时间来维护
不支持虚拟存储
最后一页仍有碎片
```

 

## 5.6 分段存储管理

### 5.6.1 分段存储管理方式的引入

便于编程

分段共享

分段保护

动态链接：在作业运行之前，并不把几个目标程序都链接起来，而是先将主程序对应的目标程序装入内存并启动运行，当运行过程中又需要调用某段时，再将该段（目标程序）调入内存并链接起来

动态增长

 

### 5.6.2 分段系统的基本原理

分段：作业的地址空间被划分成若干个段，每个段是一组完整的逻辑信息，都有自己的名字，都是从零开始编址的一段连续的地址空间，各段长度是不相等的

段表：为每个段分配一个连续的分区，进程中的各个段可以离散地分配到内存中不同的分区中。在系统中为每个进程建立一张段映射表，每个段在表中占有一表项，在其中记录了该段在内存中的起始地址和段的长度。 

地址变换机构：根据段表的起址和该段的短号计算出该段对应段表项的位置，从而读出该段在内存中的起始地址。

 

### 5.6.3 共享

 

### 5.6.4 存储管理算法

基本上同可重定位动态分区管理算法

 

### 5.6.5 评价

优点：

```
支持多道程序设计
程序不必连续存放
便于实现存储保护，段的动态增长，动态链接
```

缺点：

```
不支持虚拟存储
存在分区的移动合并操作，占用处理机时间
段的最大长度受限制
仍然存在碎片
```

 

## 5.7 段页式存储管理方式

### 5.7.1 基本原理

先将整个主存划分成大小相等的存储块，把用户程序按程序的逻辑关系划分成若干个段，并为每个段赋予一个段名，再把每个段划分成若干页，以页架为单位离散分配。在段页式系统中，其地址结构由段号、段内页号和页内地址三部分组成。

### 5.7.2 地址变换过程

首先利用段号S，将它和段长进行比较，若未越界，利用段表始址和短号求出该段对应的段表项在段表中的位置，从中得到该段的页表地址，并利用逻辑地址中的段内页号来获得对应页的页表项位置，从中读出该页所在的物理块号，再用块号b和页内地址构成物理地址。

### 5.7.3 存储管理算法

基本上同可重定位动态分区管理算法

### 5.7.4 评价

优点：

```
支持多道程序设计
程序不必连续存放
便于实现存储保护，段的动态增长，动态链接
```

缺点：

```
不支持虚拟存储，段的最大程度受限制
```

