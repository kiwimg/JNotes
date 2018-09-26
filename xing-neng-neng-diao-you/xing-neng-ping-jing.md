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

文件IO需要关注 - 磁盘IO分别在哪些盘  
、- 读IO和写IO的比例  
、- 读IO是顺序的还是随机的

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

### ![](/assets/importiostat.png)

### cpu属性值说明：

```
%user：CPU处在用户模式下的时间百分比。
%nice：CPU处在带NICE值的用户模式下的时间百分比。
%system：CPU处在系统模式下的时间百分比。
%iowait：CPU等待输入输出完成时间的百分比。
%steal：管理程序维护另一个虚拟处理器时，虚拟CPU的无意识等待时间百分比。
%idle：CPU空闲时间百分比。
```

#### disk属性值说明：

```
rrqm/s: 每秒进行 merge 的读操作数目。即 rmerge/s
wrqm/s: 每秒进行 merge 的写操作数目。即 wmerge/s
r/s: 每秒完成的读 I/O 设备次数。即 rio/s
w/s: 每秒完成的写 I/O 设备次数。即 wio/s
rsec/s: 每秒读扇区数。即 rsect/s
wsec/s: 每秒写扇区数。即 wsect/s
rkB/s: 每秒读K字节数。是 rsect/s 的一半，因为每扇区大小为512字节。
wkB/s: 每秒写K字节数。是 wsect/s 的一半。avgrq-sz: 平均每次设备I/O操作的数据大小 (扇区)。
avgqu-sz: 平均I/O队列长度。
await: 平均每次设备I/O操作的等待时间 (毫秒)。
svctm: 平均每次设备I/O操作的服务时间 (毫秒)。
%util: 一秒中有百分之多少的时间用于 I/O 操作，即被io消耗的cpu百分比
```

> 备注：如果 %util 接近 100%，说明产生的I/O请求太多，I/O系统已经满负荷，该磁盘可能存在瓶颈。如果 svctm 比较接近 await，说明 I/O 几乎没有等待时间；如果 await 远大于 svctm，说明I/O 队列太长，io响应太慢，则需要进行必要优化。如果avgqu-sz比较大，也表示有当量io在等待。

### vmsat

![](/assets/importvmstat.png)

**procs**：procs 中有 r 和 b 列，它报告进程统计信息。在上面的输出中，在运行队列（r）中有两个进程在等待 CPU 并有零个休眠进程（b）。通常，它不应该超过处理器（或核心）的数量，如果你发现异常，最好使用 top 命令进一步地排除故障。

```
r：等待运行的进程数。
b：休眠状态下的进程数。
```

**memory**： memory 下有报告内存统计的 swpd、free、buff 和 cache 列。你可以用 free -m 命令看到同样的信息。在上面的内存统计中，统计数据以千字节表示，这有点难以理解，最好添加 M 参数来看到以兆字节为单位的统计数据。

```
swpd：使用的虚拟内存量。
free：空闲内存量。
buff：用作缓冲区的内存量。
cache：用作高速缓存的内存量。
inact：非活动内存的数量。
active：活动内存量。
```

**swap**：swap 有 si 和 so 列，用于报告交换内存统计信息。你可以用 free -m 命令看到相同的信息。

```
si：从磁盘交换的内存量（换入，从 swap 移到实际内存的内存）。
so：交换到磁盘的内存量（换出，从实际内存移动到 swap 的内存）。
```

**I/O**：I/O 有 bi 和 bo 列，它以“块读取”和“块写入”的单位来报告每秒磁盘读取和写入的块的统计信息。如果你发现有巨大的 I/O 读写，最好使用 iotop 和 iostat 命令来查看。

\`bi：从块设备接收的块数。

bo：发送到块设备的块数。\`

**system**：system 有 in 和 cs 列，它报告每秒的系统操作。  
\`  
in：每秒的系统中断数，包括时钟中断。

cs：系统为了处理所以任务而上下文切换的数量。\`

**CPU：**CPU 有 us、sy、id 和 wa 列，报告（所用的） CPU 资源占总 CPU 时间的百分比。如果你发现异常，最好使用 top 和 free 命令。

```
us：处理器在非内核程序消耗的时间。
sy：处理器在内核相关任务上消耗的时间。
id：处理器的空闲时间。
wa：处理器在等待IO操作完成以继续处理任务上的时间。
```

### 网络IO

网络IO需要关注IOPS、带宽、IO的尺寸（大小），ping：最基本的，可以指定包的大小。iperf、ttcp：测试tcp、udp协议最大的带宽、延时、丢包。使用的命令有sar、ifconfig、netstat以及查看net的dev速率

| **模块** | **类型** | **度量方法** | **衡量标准** |
| :--- | :--- | :--- | :--- |
| 网络 | 使用情况 | 1、sar -n DEV 的收发计数大于网卡上限2、ifconfig RX/TX 带宽超过网卡上限3、cat /proc/net/dev 的速率超过上限4、nicstat 的util基本满负载 | 1、收发包的吞吐率达到网卡上限2、有延迟3、有丢包4、有阻塞 |
| 网络 | 满载 | 5、ifconfig dropped 有计数6、netstat -s “segments restransmited”有计数7、sar -n DEV rxdrop txdrop 有计数 | 统计的丢包有计数证明已经满载了 |
| 网络 | 错误 | 8、ifconfig, ‘errors’9、netstat -I, “RX-ERR”/”TX-ERR”10、sar -n EDEV, “rxerr/s”,”txerr/s”11、ip -s link, “errors” | 错误有计数 |



