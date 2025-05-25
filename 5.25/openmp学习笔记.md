## OpenMP 详解
 
### 一、定义
 
OpenMP 是 Open Multi-Processing 的缩写，它是一个应用程序接口（API）。通过该接口，能够显式指导多线程、共享内存的并行性操作，适用于在不同平台上，使用 C、C++ 和 Fortran 语言进行独立于平台的共享内存并行编程 。
 
### 二、核心概念
 
- 并行区域(Parallel Region)：程序中被多个线程并行执行的代码块，是并行计算的核心部分。
- 线程(Thread)：并行执行代码的基本单位，每个线程负责执行并行区域的一部分任务。
- 工作共享结构(Work-sharing Constructs)：用于在多个线程之间合理分配执行任务，确保任务高效执行。
- 同步指令(Synchronization Directives)：控制线程之间的执行顺序，避免数据竞争和不一致问题。
 
### 三、并行化原理
 
1. 高级抽象层：OpenMP 在低级多线程原语基础上提供高级抽象，其编程模型和接口具备高度可移植性，能适配不同编译器（如 GCC 4.4+、Clang 3.8+ 等现代编译器通常默认支持，无需额外安装依赖库）和硬件体系结构，可在从几核的台式机到数百核的超级计算机计算节点上运行。
2. 代码增强方式：通过使用特殊的、类似注释的编译器指令（ pragma ）增强顺序代码。开发者只需添加合适的  pragma  指令，就能轻松将现有顺序代码并行化。
3. 并行化限制场景
- 数据依赖限制：若数据处理顺序存在依赖关系，即当前处理的数据依赖前一时刻处理结果，无法实现并行化。
- 竞争条件限制：OpenMP 运行于共享内存体系，多个线程若同时操作同一数据且无有效同步机制，会导致执行结果随机；加锁虽能解决竞争但会降低性能，违背并行初衷。
 
### 四、语法及选项
 
OpenMP 的语法格式为:   
`#pragma omp <directive> [clause[[,] clause] ...] `
 
（一）directive 指令
 
- atomic：指定内存位置将被原子更新，确保对内存的操作不会被其他线程干扰。
- barrier：线程在此等待，直至所有线程都执行到该 barrier 处，实现线程同步。
- critical：其后代码块为临界区，同一时刻仅允许一个线程执行，避免资源竞争 。
- flush：保证所有线程对共享对象拥有相同的内存视图，防止数据不一致。
- for：置于 for 循环前，将 for 循环并行化，由多个线程执行（循环变量仅限整型）。
- master：指定后续程序由主线程执行。
- ordered：确保在接下来的代码块中，并行化的 for 循环按顺序执行。
- parallel：表示后续代码块将被多个线程并行各执行一遍。
- sections：将后续代码块划分为多个可并行执行的 section 块。
- single：后续程序仅在一个线程（不一定是主线程）中执行，不进行并行。
- threadprivate：指定变量为线程局部存储，每个线程拥有独立副本。
 
（二）clause 子句
 
- copyin：使  threadprivate  变量的值与主线程的值保持一致。
- copyprivate：实现不同线程中的变量在所有线程间共享。
- default：规定并行区域中未声明作用域变量的行为。
- firstprivate：线程局部存储变量的初始值为进入并行区之前的值。
- if：通过判断条件决定是否进行并行化。
- lastprivate：在循环并行执行结束后，指定变量的值为循环体顺序执行最后一次时的值；在  #pragma sections  中，为按文本顺序最后一个 section 执行取得的值 。
- nowait：忽略 barrier 的同步等待，提高执行效率。
- num_threads：设定线程数量，默认值为计算机硬件支持的最大并发数（通常是 CPU 内核数目，超线程会被操作系统视为独立内核）。
- ordered：配合  for  指令，使标记为  ordered  的部分在循环并行化时按顺序执行。
- private：指定变量为线程局部存储。
- reduction：指定一个或多个线程私有变量在并行区域结束时进行归约操作。
- schedule：设定 for 循环的并行化方法，包括  dynamic 、 guided 、 runtime 、 static  四种 ：
- schedule(static, chunk_size)：将  chunk_size  数量的循环体执行任务，静态顺序分配给各线程。
- schedule(dynamic, chunk_size)：把循环体按  chunk_size （默认值为 1）分组，线程执行完一组后重新等待分配新组。
- schedule(guided, chunk_size)：将循环体分组分配给线程，初始组包含循环体数目较大，后续按指数方式递减至  chunk_size 。
- schedule(runtime)：循环并行化方式在运行时根据环境变量  OMP_SCHEDULE  动态确定。
- shared：指定变量为所有线程共享。