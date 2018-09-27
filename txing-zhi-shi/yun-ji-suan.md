# _云计算_

自从互联网兴起以来，Server就成了网络的核心部件。所以围绕Server的生意圈，也发展得如火如荼。

从最早的电信托管，到虚拟机，到现在的Serverless，形成了几大阵容：

1、IaaS（基础设施即服务：Infrastructure as a Service）

2、PaaS（平台即服务：Platform as a Service）

3、SaaS（软件即服务：Software as a Service）

IaaS是包硬不包软，面对集成商，PaaS是包硬包软不包工，面对开发者，SaaS是全包，面对消费者。

![](/assets/importyunjs.png)

三大阵营都在不断演进中，互相取长补短，甚至模糊了彼此的界限。

PaaS最新的发展就是：

1、BaaS（后端即服务：Backend as a Service）

2、Faas（函数即服务：Functions as a Service）

#### 这两种架构被称为Severless

BaaS与FaaS这两种架构被称为Severless，并非对开发者而言，是对服务商而言，没有一直运行的定制服务存在，不占用服务商的计算资源。同共享单车有些类似，是计算机分时租赁方式，按次按时计价。



BaaS并不存放客户代码，只提供通用的逻辑，产品的逻辑都需要在富客户端完成。这些通用的逻辑为所有客户共享，因而不浪费服务商的计算资源，也就可以做到按API调用次数计算费用。

以前叫我们把二层的富客户端都改成三层瘦客户端，现在搞个共享数据库，又叫我们改成富客户端。横竖赚钱。

