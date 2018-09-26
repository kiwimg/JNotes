# _性能瓶颈_

## CPU分析

当程序响应变慢的时候，首先使用top、vmstat、ps等命令查看系统的cpu使用率是否有异常，从而可以判断出是否是cpu繁忙造成的性能问题。其中，主要通过us（用户进程所占的%）这个数据来看异常的进程信息。当us接近100%甚至更高时，可以确定是cpu繁忙造成的响应缓慢。一般说来，cpu繁忙的原因有以下几个：

* 线程中有无限空循环、无阻塞、正则匹配或者单纯的计算
* 发生了频繁的gc
* 多线程的上下文切换

CPU使用率分为用户态cpu使用率和系统态cpu使用率，所以提高系统的性能的一个目标就是降低系统cpu的使用率，通过是vmstat 命令查看us 和sy 、监控cpu的调度程序运行队列。

## 内存分析

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

## IO分析

### 文件IO

使用系统工具pidstat、iostat、vmstat来查看io的状况

#### pidstat

pidstat主要用于监控全部或指定进程占用系统资源的情况，如CPU，内存、设备IO、任务切换、线程等。pidstat首次运行时显示自系统启动开始的各项统计信息，之后运行pidstat将显示自上次运行该命令以后的统计信息。用户可以通过指定统计的次数和时间来获得所需的统计信息。

CentOS/Fedora/RHEL版本的linux中则使用下面的命令：

yum install sysstat

常用的参数：

* -u：默认的参数，显示各个进程的cpu使用统计
* -r：显示各个进程的内存使用统计
* -d：显示各个进程的IO使用情况
* -p：指定进程号
* -w：显示每个进程的上下文切换情况
* -t：显示选择任务的线程的统计信息外的额外信息
* -T { TASK \| CHILD \| ALL }

```
pidstat -d
```

![](/assets/importiopid.png)

报告IO统计显示以下信息：

* PID：进程id
* kB\_rd/s：每秒从磁盘读取的KB
* kB\_wr/s：每秒写入磁盘KB
* kB\_ccwr/s：任务取消的写入磁盘的KB、当任务截断脏的pagecache的时候会发生。
* COMMAND:task的命令名

##### 查看特定进程的CPU使用情况

指令：`pidstat –u –p {pid} {interval} [count]`  
例子：``pidstat -u –p `pgrep admin` 1 10``

###### 查看特定进程的Memory使用情况

指令：pidstat –r –p {pid} {interval} \[count\]

###### 查看特定进程的IO使用情况

指令：pidstat –d –p {pid} {interval} \[count\]

#### iostat

iostat是I/O statistics（输入/输出统计）的缩写，用来动态监视系统的磁盘操作活动

用法：iostat \[ 选项 \] \[ &lt;时间间隔&gt; \[ &lt;次数&gt; \]\]

常用选项说明：

```css
-c：只显示系统CPU统计信息，即单独输出avg-cpu结果，不包括device结果
-d：单独输出Device结果，不包括cpu结果
-k/-m：输出结果以kB/mB为单位，而不是以扇区数为单位
-x:输出更详细的io设备统计信息
interval/count：每次输出间隔时间，count表示输出次数，不带count表示循环输出`
```

### 网络IO



