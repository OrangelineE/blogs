# What is OS
OS 是一种`系统软件`
+ 与硬件交互
+ 与资源共享进行调度管理
+ 解决并发操作处理中存在的协调问题
+ 数据结构复杂，外部接口多样化，便于用户反复使用

## What OS does
+ 管理与配置内存
+ 决定系统资源供需的优先次序
+ 控制输入与输出设备
+ 操作网络与管理文件系统等基本事务
+ 提供一个让用户与系统交互的操作界面

## The aim for OS
+ 管理系统资源（硬件资源）
  + `有效性`
    + 提高系统资源利用率
    + 提高系统吞吐量 -> 提高并发可提高系统吞吐量，而并行是有物理极限的（CPU的数量）
+ 方便用户使用
  + 方便性
+ 作为扩充机器
  + 可扩充性
  + 开放性 （兼容各种软件，标准等）
  
## Its Functionality
+ 作为计算机系统`硬件`资源的管理者
  + CPU管理
    + 进程控制
    + 进程同步
    + 进程通信
    + 调度
  + 存储器管理
    + 内存分配
    + 内存保护
    + 地址映射
    + 内存扩充
  + I/O设备管理
    + 缓冲管理
    + 设备分配
    + 设备处理
  + 文件管理
    + 文件存储空间的管理
    + 目录管理
    + 文件的读/写管理和保护
+ 作为用户与计算机之间的`接口`
  + 程序接口
  + 命令接口
  + GUI（图形用户接口）
+ 对计算机资源的`抽象`
  + 将具体的计算机硬件资源抽象成软件资源，方便用户使用
  + 开放了简单的访问方式，隐藏了实现细节

## Its Feature
+ `并发（Concurrency）`
  + 在同一`时间间隔`内执行和调度多个程序的能力
    + 宏观上，CPU同时执行多道程序
    + 微观上，CPU在多道程序之间高速切换（分时交替执行）
    + 关注单个CPU同一时间段内处理任务数量的能力
    + 注：并行是指同一`时间点`发生的事件数量
+ 共享（Sharing）
  + 资源共享，系统中的资源供多个`并发执行`的应用程序共同使用
    + 同时访问方式：同一时段允许多个程序同时访问共享资源
    + 互斥共享方式：也称独占式，允许多个程序在同一个共享资源上独立而互不干扰的工作
    + 共享打印机，音频设备，视频设备
+ 虚拟 (Virtual)
  + 使用某种技术把一个物理实体变成多个逻辑上的对应物
    + 时分复用技术（TDM）
      + 虚拟CPU：四核八线程
      + 虚拟设备技术：虚拟打印机
    + 空分复用技术 (SDM)
      + 虚拟磁盘：将一块磁盘虚拟成若干个卷
      + 虚拟存储器技术
+ 异步 (Asynchronism)
  + 在多道程序环境下，允许多个程序并发执行
  + 在单处理机环境下，多个程序分时交替执行
    + 程序执行的不可预知性
      + 因何暂停
      + 每道程序需要多少时间
      + 不同程序的性能，比如计算多少，I/O多少
    + 宏观上“一气呵成”，微观上“走走停停”
  
`并发和共享互为存在条件`
+ 共享性要求OS中同时运行多道程序
+ 并发性难以避免的导致多道程序同时访问同一个资源，若多道程序无法共享部分资源（如磁盘），则无法并发
`虚拟的前提也是并发`
## OS 运行机制
### 基本概念
内核程序 <---> 应用程序
核心态 <---> 用户态
特权指令 <---> 非特权指令
### 时钟管理
+ 计时：提供系统时间
+ 时钟中断：比如进程/线程切换
### 中断机制
+ 提高多道程序环境下CPU利用率
+ 外中断：中断信号来源于-> 外部设备
+ 内中断：中断信号来源于-> 当前指令
  + Trap：由应用程序主动引发 (system call)
  + Fault: 由错误条件引发 (page fault)
  + Abort: 由致命错误引发
+ 中断处理过程
  + 产生中断
    1. 关中断（CPU不响应高级中断请求）
    2. 保存断点
    3. 引出中断服务程序
    4. 保存现场和屏蔽字
    5. 开中断
  + 执行中断服务程序
  + 从中断返回，继续执行之前的函数
    1. 关中断
    2. 回复现场和屏蔽字
    3. 开中断

+ 保存现场：

  当处理器响应中断时，它必须保存当前的执行状态，以便在处理完中断后能够正确地恢复继续执行被中断的程序。保存现场通常包括：
  + 程序计数器（PC）：指向正在执行的指令地址。
  + 处理器寄存器的内容，包括通用寄存器、状态寄存器（如标志寄存器）等。
  + 栈指针（SP）：指向当前栈顶的指针。

  这些信息通常会被保存到栈中，以便中断处理程序可以在完成后恢复这些信息，继续执行原来的程序。

+ 屏蔽字（也称为中断屏蔽字）：

屏蔽字是一组位（bit），每个位对应于一个特定的中断源，用于控制哪些中断源被允许响应或被屏蔽。

如果一个特定的中断源的屏蔽位被设置为1，那么来自这个中断源的中断请求将被忽略（即“屏蔽”），处理器不会响应这个中断。

这种机制允许操作系统或中断处理程序根据需要控制中断的优先级，防止低优先级的中断干扰当前的高优先级任务。

简而言之，在中断机制中，“保存现场”是指保存当前执行状态以便恢复，而“屏蔽字”是用于控制哪些中断信号可以打断当前的处理流程。

### 原语 (Primitive)

A primitive typically refers to a basic, low-level operation provided by the OS that serves as a building block for more complex operations. These primitives are designed to be simple and atomic, meaning they are executed as a single, indivisible step that `cannot be interrupted`.

+ Atomicity: Primitives execute as a single, uninterruptible operation.
+ Low-Level: They provide fundamental, hardware-level functions.
+ Efficiency: Optimized for minimal overhead and fast execution.
+ Safety: Operate in protected mode, ensuring system stability.
+ Consistency: Maintain system state, crucial for synchronization.
+ Modularity: Can be combined to build complex operations.
+ Determinism: Produce predictable outcomes, important for reliability.

### System call
+ 由操作系统实现，给应用程序调用
+ 是一套接口的集合
+ 应用程序访问内核服务的方式

### 传统大内核结构
1. 无结构OS
2. 模块化结构OS
3. 分层式结构OS

### 微内核OS结构
足够小的内核，只实现OS核心功能
+ 与硬件处理紧密相关的部分，比如硬件处理，客户与服务器通信（client/server mode）等
+ 应用“机制与策略分离” 原理
+ 采用面向对象技术

优点
+ 提高OS的可扩展性，可靠性，可移植性
+ 支持分布式系统
+ 融入面向对象技术
缺点
+ 相较于早期OS，降低了一定效率