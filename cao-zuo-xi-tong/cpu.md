# _cpu_

  
![](blob:file:///45ec0e69-3c82-4b64-b29d-607e1a15d9ca)

现代CPU的缓存结构一般分三层，L1，L2和L3。如下图所示：级别越小的缓存，越接近CPU， 意味着速度越快且容量越少。

L1是最接近CPU的，它容量最小，速度最快，每个核上都有一个L1 Cache\(准确地说每个核上有两个L1 Cache， 一个存数据 L1d Cache， 一个存指令 L1i Cache\)；  
L2 Cache 更大一些，例如256K，速度要慢一些，一般情况下每个核上都有一个独立的L2 Cache；  
L3 Cache是三级缓存中最大的一级，例如12MB，同时也是最慢的一级，在同一个CPU插槽之间的核共享一个L3 Cache。

  
![](/assets/importcuc.png)

