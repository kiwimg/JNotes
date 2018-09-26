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

