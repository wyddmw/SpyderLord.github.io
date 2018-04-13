---
layout: post
category: dump
title: linux动态内存管理
description: 嵌入式三级项目记录
---

## 内存管理的方式
　　在强实时系统中，为了减少内存分配在时间上可能带来的不确定性，可以采用静态分配的内存管理方法。在静态分配方式中，系统在启动前，所有的任务都获得了所需要的所有内存，运行过程中，不会有新的内存请求。对于这种方式，不需要操作系统进行专门的内存管理操作，但是系统的使用内存的效率会比较低。所以静态分配方式只适合于那些强实时、应用比较简单和任务数量可以静态确定的系统。为此，大多数系统还是会采用内存的动态分配方式。
### 固定大小存储区管理
　　固定大小存储区管理和可变大小存储区管理为动态内存的常用管理方法。固定大小存储区和可变大小存储区都是指定边界的一块地址连续的内存空间，其中固定大小存储区管理实现固定大小的内存块的分配。可变大小存储区管理实现可变大小内存块的分配。<br>
　　固定大小存储区管理中，可供使用的是一段连续的内存空间被称为是一个分区。分区的大小是由大小固定的内存块来决定的。分区的大小是内存块大小的整数倍。分区分配表存储的数据结构为链表，从书上的链表的示意图来看应该是双向链表。<br>
　　使用固定大小的内存管理方式存在的缺点是内存的利用率不高。
#### 分区的操作有如下的操作：
- 创建分区
- 删除分区
- 从分区中得到内存块 
- 把内存块释放到分区
- 获取分区ID
- 获取当前创建的分区的数量
- 获取当前所有分区的ID
- 获取分区的信息
#### 分区分配表的结构体包含的变量：
```C
//描述一个空闲块的数据结构
struct free_block_type{
      int size;                                 //应该是描述这个空闲块的大小
      int start_addr;                           //内存块的起始地址
      struct free_block_type *next;             //后继节点的指针
}; 
//分区可采用的数据结构
typedef struct 
{
      PartitionID       ID;                     //分区的ID
      PatitionName      Name;                   //分区的名字
      void              *starting_address;      //分区的起始地址
      int               length;                 //分区的长度
      int               buffer_size;            //内存块的大小
      PatitionAttribute attribute;              //属性
      int               number_of_used_blocks;  //剩余内存块的数量
      Memory_chain      memory;                 //内存块链     
}
```
### 可变大小存储区管理
　　可变大小存储区的管理是基于堆的管理方式。堆为一段连续的、大小可配置的内存空间，用来提供可变内存块的分配。可变内存块称为段，最小分配单位称为页，即段的大小是页的大小的是整数倍。
```C
typedef struct 
{
      HeapID            ID;                     //堆的ID
      HeapName          name;                   //堆的名字
      TaskQueue         WaitQueue;              //等待队列
      void              *starting_address;      //内存空间的起始地址
      int               length;                 //内存空间长度
      int               page_size;              //页长度（字节）
      int               maximum_segent_size;    //最大可用段的大小
      RegionAttribute   attribute;              //堆的属性
      int               number_of_used_blocks;  //分配的块数
      HeapMemoryChain   memory;                 //堆头控制结构
} 
```
　　使用可变大小存储管理方式的缺点是最后会产生很多内存碎片，这些碎片又分为内部碎片和外部碎片。内部碎片是指分配给用户，用户未能使用的存储区，比如分配给用户8K的内存空间，但是用户的任务只使用了其中的4K的空间，剩下的4K的内存空间称为内部碎片。外部碎片是指有空闲的内存区，但是这些空闲的内存区不能满足用户的作业需求，这样的内存区称为外部碎片。<br>
#### 在动态内存空间分配的方式中如何通过页表进行绝对物理的寻找
　　通过（p，d）数对来进行绝对物理的寻找。在此之前需要补充页和块的概念。<br>
　　页：将用户相对地址空间划分成一个一个长度相等的存储区，称为页。<br>
　　块：将物理空间划分成长度相等的一个个存储区，称之为块<br>
　　页的大小由块的大小来决定。页的大小和块的大小是相等的。绝对地址=相对地址/页的大小=p...d，通过页表我们可以通过p找到对应的块，然后通过d找到对应的页内偏移，页表可以找到对应的块的绝对物理地址的起始地址，在这个的基础上通过基址+变址的方式进行绝对物理的寻址。
### C语言相关程序的描述

```C
struct allocated_block        //每个进程分配到的内存块的描述
{
      int pid;
      int size;
      int start_addr;
      char process_name[PROCESS_NAME_LEN];
      struct allocated_block *next;                   /** 因为编译的平台是确定是，所以指针的长度是确定的，不会无限循环下去。在链表和树的数据结构中就都使用到这个技巧，自身的结构体指针指向下一个节点或者下一个树的地址 */
}
struct allocated_block *allocated_block_head=NULL;    //进程分配内存块链表的首指针 

struct allocated_block *find_process(int id):         //返回值是指针
{
      struct allocated_block *p;
      p=allocated_block_head:
      while(p!=NULL)
      {
            if(p->pid==id)
            {
                  return p;
            }
      }
      return NULL;
}
```
### 内存分配的算法：
- FF（first fit）——从空闲分区表的第一个表目起查找该表，把最先能够满足要求的空闲区分配给作业，这种方法的目的在于减少查找时间。<br>
- BF（best fit）——从全部空闲区中找出能满足作业要求的，且大小最小的空闲分区，这种方法能使碎片尽量小。

