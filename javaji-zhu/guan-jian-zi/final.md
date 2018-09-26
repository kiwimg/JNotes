# _final_

在Java中基本可以修饰面向对象的绝大部分元素。我们可以通过如下的代码段来看**final**修饰的部分。

**1.修饰类（Class）**

`public final class SystemUtils {...}`

`public class OuterClass { final class InnerClass{...} }`

**2.修饰方法（Method）**

`public final void foo() {...}`

**3.修饰域（Field）**

`public final int fee = 25;`

`private static final float POINT_X = 2.6f;`

`public void foo() { final int type = 3; }`

**4.修饰方法参数（Method Argument）**

`public void foo(final int x, final int y) {...}`

在Java中，final关键字可以用来修饰类、方法和变量（包括成员变量和局部变量）

当用final修饰一个类时，表明这个类不能被继承。也就是说，如果一个类你永远不会让他被继承，就可以用final进行修饰。但是要注意final类中的所有成员方法都会被隐式地指定为final方法。

对于一个final变量，如果是基本数据类型的变量，则其数值一旦在初始化之后便不能更改；如果是引用类型的变量，则在对其初始化之后便不能再让其指向另一个对象。

下面这段话摘自《Java编程思想》第四版第143页：

> “使用final方法的原因有两个。第一个原因是把方法锁定，以防任何继承类修改它的含义；第二个原因是效率。在早期的Java实现版本中，会将final方法转为内嵌调用。但是如果方法过于庞大，可能看不到内嵌调用带来的任何性能提升。在最近的Java版本中，不需要使用final方法进行这些优化了。“

因此，如果只有在想明确禁止 该方法在子类中被覆盖的情况下才将方法设置为final的。

> 注：类的private方法会隐式地被指定为final方法。



