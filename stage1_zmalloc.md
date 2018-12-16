## [内存分配策略](https://blog.csdn.net/junlon2006/article/details/77854898)
    一个优秀的内存分配器应该具有以下特征：
    1. 分配一定大小的内存，空间额外损耗尽量较少 
        与内存管理相关的数据结构大小相关：指针大小 union结构...
    2. 分配速度尽可能快
        应用层维护多内存空闲区，复用释放的内存，降低向系统申请内存的次数
        提升内存寻址效率：与管理结构的内存布局相关。CPU的预读取 高速缓存的命中率
    3. 尽量避免内存碎片。 内存内部碎片和外部碎片
    4. 缓存本地化友好
    5. 通用性、兼容性、可移植性、易调试
    
### zmalloc.h 通过不同的宏定义决定使用不同的内存分配策略
    程序启动时通过LD_PRELOAD环境变量使用不同的内存分配策略，并不需要重新编译程序
    动态库加载优先级：LD_PRELOAD > LD_LIBRARY_PATH > /etc/ld.so.cache > /lib > /usr/lib
    系统提供的内存分配接口：
        大内存使用mmap和unmmap  小内存使用brk
**glibc的ptmalloc** glibc提供的malloc和free系列函数,多线程情况下锁争用明显，性能较差。
    
    ptmalloc将相似大小的chunk用双端链表连接起来，一个链表称为一个bin。ptmalloc维护了128个bin，并使用数组进行存储。
    数组中的第一个bin为unsorted bin， 2-64为small bins(chunk固定大小，相差8B)，65-128为large bins(给定范围，chunk按照大小和最近使用顺序排列)
    ·主分配区和非主分配区(main_arena non_main_arena)
        主分配区访问进程的mmap和heap区域，使用mmap和brk申请虚拟内存，非主分配区只能使用mmap向系统申请内存
    ·fast_bins 不大于64B(max_fast)的内存释放后，首先放到fast_bins
    ·unsorted_bins 用户释放的内存大于max_fast或者fast_bins中的空闲chunk合并后，首先放到unsorted_bin
        分配内存时的查找顺序 fast_bin > unsorted_bin > bins数组
        当unsorted_bin中的内存不满足时，先将其中的chunk加入bins再从bins数组中分配
        unsorted_bin相当于bins的一个缓冲区，加快分配的速度
    · top chunk对于主分配区和非主分配区是不同的
        non_main_arena使用mmap分配较大内存管理多个sub-heap,地址分配由低到高，高地址空闲chunk叫做top chunk
            fast_bin > unsorted_bin > bins > top_chunk > sub-heap
        main_arena唯一能够映射进程heap区域的分配区，使用sbrk()增大或收缩进程heap的大小。top chunk在heap高地址出
            fast_bin > unsorted_bin > bins > top_chunk > heap(通过sbrk()系统调用增大heap上限)
    · mmaped chunk
        分配空间过大，直接使用mmap分配内存(默认128kb)
        
    内存回收总结：
    1. 大内存直接mmap unmmap
    2. 小内存(与top chunk相邻):如果与
    
**google的tcmalloc**
    
    tcmalloc 是google开发的内存分配器。golang中也使用了类似的内存管理算法
**fb的jemalloc**
    
    jemalloc