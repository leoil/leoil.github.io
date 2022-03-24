C++ 内存管理
参考： https://blog.csdn.net/zju_fish1996/article/details/108858577

<!-- TOC -->

- [1. 基础概念](#1-基础概念)
  - [1.1. 内存布局](#11-内存布局)
  - [1.2. 函数栈](#12-函数栈)
  - [1.3. 全局变量](#13-全局变量)
  - [1.4. 内存对齐](#14-内存对齐)
  - [1.5. 内存碎片](#15-内存碎片)
  - [1.6. 继承类布局](#16-继承类布局)
  - [1.7. 字节序(endianness)](#17-字节序endianness)
  - [1.8. POD类型](#18-pod类型)
- [2. 操作系统](#2-操作系统)
  - [SIMD](#simd)
  - [高速缓存(Cache)](#高速缓存cache)
  - [虚拟内存](#虚拟内存)
  - [置换算法](#置换算法)
- [C++语法](#c语法)
  - [位域(Bit Field)](#位域bit-field)
  - [new与placement new](#new与placement-new)

<!-- /TOC -->


# 1. 基础概念
## 1.1. 内存布局 
![image.png](http://ww1.sinaimg.cn/large/a0e70468ly1gojq108f3oj20bj0ait8x.jpg)
* Code Segment
  
* Data Segment
  
* BSS(Block started by symbol)
  
* Heap  
  **从低地址向高地址增长。**
  动态分配的内存
  
* Stack  
  **从高地址向低地址增长。**
  编译器自己管理. 函数与局部变量\返回变量等(包括main函数)

## 1.2. 函数栈
可执行文件: BSS + Data Segment + Code Segment

调用函数的过程:
    调用时 一块连续内存(帧)入栈; 

堆栈帧:  
    ![image.png](http://ww1.sinaimg.cn/large/a0e70468ly1gojq9xdcgnj20a60c6q35.jpg)  
    包括内容: 函数参数/局部变量/寄存器值备份/返回地址  
    在函数调用时就被创建(局部变量在调用时就分配好了空间)


> * **栈:** CPU寄存器里某个指针(通常时ESP/RSP寄存器)指向的**一片内存区域**
>   
> * **出栈/入栈:** 对应寄存器值加/减  
> 
> * **栈帧:** 是编译器用来实现过程/函数调用的一种数据结构, 利用寄存器`EBP`访问局部变量  
>&nbsp; 函数的调用会在call stack上维护一个(新的)独立的stack frame(包括:返回地址与参数\临时变量\调用的上下文),   
ESP始终指向最新的栈帧顶部,EBP用户访问栈中的数据(EBP指向先前的EPB, +4得到函数调用链表)  
>![undefined](http://ww1.sinaimg.cn/large/a0e70468ly1gojr3q8tzgj20lf0iit9x.jpg)  
>参考:https://zhuanlan.zhihu.com/p/77663680
具体调用过程, 参考: https://zhuanlan.zhihu.com/p/313608043

## 1.3. 全局变量
未初始化:  存在BSS段  

初始化:   存在Data Segment段

## 1.4. 内存对齐
![undefined](http://ww1.sinaimg.cn/large/a0e70468ly1gojsc2cn86j20tc05emx3.jpg)  
OS在内存中存放数据的首地址未k(4/8)的倍数
>参考: https://zhuanlan.zhihu.com/p/30007037
>对齐系数/有效对齐值
对齐规则: 
       定义有效对齐值（alignment）为结构体中 最宽成员 和 编译器/用户指定对齐值 中较小的那个。

(1) 结构体起始地址为有效对齐值的整数倍

(2) 结构体总大小为有效对齐值的整数倍

(3) 结构体第一个成员偏移值为0，之后成员的偏移值为 min(有效对齐值, 自身大小) 的整数倍相当于每个成员要进行对齐，并且整个结构体也需要进行对齐

## 1.5. 内存碎片
* 内部碎片: 系统分配内存大于所需内存

* 外部碎片: 内存不断回收再分配, 分布很散, 使得大内存不能再分配
  
## 1.6. 继承类布局
* 子类的数据会位于父类之后

* 含**虚函数**的类
    在类最前端占用4个字节, 用于存储虚表指针(vpointer), 指向虚函数表(vtable)

## 1.7. 字节序(endianness)
* 大端  (little_endian)：低位有效字节存储于较低的内存位置  
  
* 小端  (big_endian)：高位有效字节存储于较低的内存位置  
![undefined](http://ww1.sinaimg.cn/large/a0e70468ly1gojtjhwtmlj20bf06cglq.jpg)
跨平台需要考虑大小端, 

## 1.8. POD类型
C++中, 如果类中包含指针或容器(内存时单独管理), 内存一般不连续.

**POD类型**:   trival + standard layout
* trival: 对象储存在定义时就完成了对象的生存期, 无需构造函数;

* std layout: 内存排布有序

频繁引用的仅用于记录新的的数据结构, 被设计成POD类型以提高效率.

> 对象生存期: 
> 
> * 静态生存期:  
>   与程序运行期相同
>   namespace中声明的变量/函数内部声明+static/
>   不会因函数调用产生副本, 也不会因返回消失, 可以共享.
> 
> * 动态生存期:  
>   除静态外全部. 诞生于声明点, 结束于声明块执行完毕, 与对象生存期一致.
>
> 类的静态成员:(static)
> 可以不用初始化对象, 直接访问
> 
> * 静态数据成员
>   不属于任何对象
> 
> * 静态函数成员
>   一般情况下，静态函数用来访问类的静态数据成员。

# 2. 操作系统
## SIMD
Single Instruction Multiple Data: 用一个指令并行对多行数据进行运算(CPU基本指令集)

## 高速缓存(Cache)
L1/L2/L3 缓存:  
* CPU请求数据:
  在缓存中直接载入register; 不在再从内存中请求
  
* CPU写入数据:
  1. write through cache
    写入时cahce, 同时写入memory

  2. write-bcak
    仅写入cache, 必要时再写入memory
![undefined](http://ww1.sinaimg.cn/large/a0e70468ly1goju7ax5qyj20g605z0ss.jpg)  

"命中"---> 程序读写性能, 因此需要尽可能连续访问内存(locality): 时间与空间上

## 虚拟内存
把不连续的物理内存块映射到虚拟地址空间(virtual address space). 对用户程序而言, 内存页是连续的. 出于安全性等考虑, 程序都是运行在虚拟的内存空间上.

**页**: 虚拟内存中, 系统会通过MMU将虚拟地址映射到物理内存, 每个程序的地址空间被划分成多少块, 每个内存块就被成为页. 每个页中包含连续的地址, 并被映射到物理内存中.

**MMU**:  MemoryManagementUnit 转换虚拟地址与真实物理地址
如: TLSF算法

**DRAM**:
在这里，我们将CPU，高速缓存和主存视为一个整体，统称为DRAM。由于DRAM与磁盘之间的读写也比较耗时，为了提高程序性能，我们依然需要确保自己的程序具有良好的“局部性”——在任意时刻都在一个较小的活动页面上工作

因此: 每个程序的地址空间, 对应着一个虚拟地址和物理地址, 两者需要进行地址翻译.

* 缺页
  方位到了不属于物理内存中的页(内存不足): 系统从磁盘将对应内容装载到物理内存, 同时也可能将页写回磁盘.
  
* 分页
    虚拟内存的内存块: 页
    物理内存的内存块: 页框
    大小一致: DRAM与磁盘以页为单位进行交换
    
    虚拟内存与物理地址的翻译： 先从TLB(Transition Lookaside Buffer)的设备中查找, 如果找不到, 再查找虚拟地址中的虚拟页号和偏移量,通过虚拟页号找到页框号. 通过偏移量在对应页框中的偏移, 得到物理地址.

    为加速，使用多级页表，倒排页表等结构
## 置换算法
空间换时间: 在有限的空间内存储快速查询的结构. 
为提高命中, 需要不断更新储存的内容, 即置换.

常见的置换:
* 最近未使用置换(NRU)
  出现未命中时, 置换最近一个周期内未使用的数据
* 先入先出置换(FIFO)
  MISS: 置换最早出现的数据
* 最近最少使用置换(LRU)
  MISS: 置换未使用时间最长的数据

# C++语法
## 位域(Bit Field)
类和结构体内可以存在occupy less storage than an integral type的member.  

这些members以bit fields的形式被声明. Bit-field member-declarator的声明语法为:

``declarator : constant-expression``
```cpp
// bit_fields1.cpp
// compile with: /LD
struct Date {
   unsigned short nWeekDay  : 3;    // 0..7   (3 bits)
   unsigned short nMonthDay : 6;    // 0..31  (6 bits)
   unsigned short nMonth    : 5;    // 0..12  (5 bits)
   unsigned short nYear     : 8;    // 0..100 (8 bits)
};
```

![undefined](http://ww1.sinaimg.cn/large/a0e70468ly1gouzeddpqsj20cg038jr5.jpg)

## new与placement new
**new**: C++中用于动态分配内存的运算符, 包含以下两个操作:
1. 调用`operator new()`函数, 动态分配内存
   
2. 在分配的动态内存上调用构造函数, 以初始化相应类型的对象,并返回首地址

**new的语法**
```cpp
::(optional) new (placement_params)(optional) ( type ) initializer(optional)
```
* 一般表达式
  `var = new type(initialzer);`
* 对象数组表达式
```cpp
vat = new type[size];
delete[] var;
```
* 不抛出异常表达式
```cpp
auto p = new double[2][2];
```
* 占位符类型
  placeholder type(auto/decltype)
auto p = new auto('c')
* 带位置的表达式  
