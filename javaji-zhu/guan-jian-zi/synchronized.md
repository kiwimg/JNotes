# _synchronized_

线程安全问题，即多个线程同时访问一个资源时，会导致程序运行结果并不是想看到的结果。这里面，这个资源被称为：临界资源（也有称为共享资源），基本上所有的并发模式在解决线程安全问题时，都采用“序列化访问临界资源”的方案，即在同一时刻，只能有一个线程访问临界资源，也称作同步互斥访问。

> java 中的**synchronized**可以保证方法或者代码块在运行时，同一时刻只有一个方法可以进入到临界区，同时它还可以保证共享变量的内存可见性

Java中每一个对象都可以作为锁，这是synchronized实现同步的基础：

1. 普通同步方法，锁是当前实例对象
2. 静态同步方法，锁是当前类的class对象
3. 同步方法块，锁是括号里面的对象

## 原理

我们先看一段简单的代码：

```java
public class SynchronizedTest {
    public synchronized void test1(){

    }

    public void test2(){
        synchronized (this){

        }
    }
}
```

利用javap工具查看生成的class文件信息来分析Synchronize的实现

![](/assets/importsynyanli.png)

从上面可以看出，同步代码块是使用**monitorenter**和**monitorexit**指令实现的，同步方法（在这看不出来需要看JVM底层实现）依靠的是方法修饰符上的ACC\_SYNCHRONIZED实现。

同步代码块：monitorenter指令插入到同步代码块的开始位置，monitorexit指令插入到同步代码块的结束位置，JVM需要保证每一个monitorenter都有一个monitorexit与之相对应。任何对象都有一个monitor与之相关联，当且一个monitor被持有之后，他将处于锁定状态。线程执行到monitorenter指令时，将会尝试获取对象所对应的monitor所有权，即尝试获取对象的锁；

同步方法：synchronized方法则会被翻译成普通的方法调用和返回指令如:invokevirtual、areturn指令，在VM字节码层面并没有任何特别的指令来实现被synchronized修饰的方法，而是在Class文件的方法表中将该方法的access\_flags字段中的synchronized标志位置1，表示该方法是同步方法并使用调用该方法的对象或该方法所属的Class在JVM的内部对象表示Klass做为锁对象。\(摘自：[http://www.cnblogs.com/javaminer/p/3889023.html](http://www.cnblogs.com/javaminer/p/3889023.html)\)

## 了解两个重要的概念：Java对象头，Monitor

### Java对象头

HotSpot虚拟机中，对象在内存中存储的布局可以分为三块区域：对象头（Header）、实例数据（Instance Data）和对齐填充（Padding）。 对象头主要包括两部分数据：**Mark Word（标记字段）**、**Klass Pointer（类型指针）**



