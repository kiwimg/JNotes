# _性能瓶颈_

### CPU分析

当程序响应变慢的时候，首先使用top、vmstat、ps等命令查看系统的cpu使用率是否有异常，从而可以判断出是否是cpu繁忙造成的性能问题。其中，主要通过us（用户进程所占的%）这个数据来看异常的进程信息。当us接近100%甚至更高时，可以确定是cpu繁忙造成的响应缓慢。一般说来，cpu繁忙的原因有以下几个：

* 线程中有无限空循环、无阻塞、正则匹配或者单纯的计算
* 发生了频繁的gc
* 多线程的上下文切换

CPU使用率分为用户态cpu使用率和系统态cpu使用率，所以提高系统的性能的一个目标就是降低系统cpu的使用率，通过是vmstat 命令查看us 和sy 、监控cpu的调度程序运行队列。

### 内存分析

对Java应用来说，内存主要是由堆外内存和堆内内存组成。

#### 堆外内存

主要是JNI、Deflater/Inflater、DirectByteBuffer（nio中会用到）使用的。对于这种堆外内存的分析，还是需要先通过vmstat、sar、top、pidstat等查看swap和物理内存的消耗状况再做判断的。此外，对于JNI、Deflater这种调用可以通过Google-preftools来追踪资源使用状况。

#### 堆内内存

堆内内存此部分内存为Java应用主要的内存区域。通常与这部分内存性能相关的有：

* 创建的对象：这个是存储在堆中的，需要控制好对象的数量和大小，尤其是大的对象很容易进入老年代
* 全局集合：全局集合通常是生命周期比较长的，因此需要特别注意全局集合的使用
* 缓存：缓存选用的数据结构不同，会很大程序影响内存的大小和gc
* ClassLoader：主要是动态加载类容易造成永久代内存不足
* 多线程：线程分配会占用本地内存，过多的线程也会造成内存不足

以上使用不当很容易造成：

* 频繁GC -&gt; Stop the world，使你的应用响应变慢
* OOM，直接造成内存溢出错误使得程序退出。OOM又可以分为以下几种：
* Heap space：堆内存不足
* PermGen space：永久代内存不足
* Native thread：本地线程没有足够内存可分配

排查堆内存问题的常用工具是jmap，是jdk自带的

内存与swap发生交换时候问题，通过vmstat监控si so有没有页面调度。


