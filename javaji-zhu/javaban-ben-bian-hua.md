# _java版本变化_

## java5

* 泛型
* 增强循环（Enhanced for Loop）
* 自动封箱拆箱\(Autoboxing/Unboxing \)。八大基本类型和它们的包装类能够自动的相互转换
* 枚举\(Typesafe Enums\)
* 可变参数 \(Varargs\)
* 静态导入（Static Import）。通过import类来使用类里的静态变量或方法（直接通过名字，不需要加上类名.）,简化了代码的书写。ps:过去的版本中只能通过继承类或实现接口才能使用。

* 注解（Annotations）。关键字@interface。

* 新的线程模型和并发库（java.util.concurrent\)

## java6

* 集合框架增强。
  为了更好的支持双向访问集合。添加了许多新的类和接口。

> 新的数组拷贝方法。Arrays.copyOf和Arrays.copyOfRange
>
> //以下为添加的新接口和类
>
> Deque,BlockingDeque,NavigableSet,NavigableMap,ConcurrentNavigableMap，ArrayDeque， ConcurrentSkipListSet ,ConcurrentSkipListMap,ConcurrentSkipListMa

* Scripting. 可以让其他语言在java平台上运行。 java6包含了一个基于Mozilla Rhino实现的javascript脚本引擎。

* 支持JDBC4.0规范。

## java7

* 数字字面量的改进

> 1、添加二进制表示  
> Java7之前支持十进制（123）、八进制（0123）、十六进制（0X12AB）  
> Java7添加二进制表示（0B11110001、0b11110001）二进制前缀0b或者0B。整型（byte, short, int, long）可以直接用二进制表示。
>
> 2、字面常量数字的下划线。
>
> 用下划线连接整数提升其可读性，自身无含义，不可用在数字的起始和末尾。
>
> int k = 1\_1;//值为11

* switch中添加对String类型的支持。

* try-with-resources

* 捕获多个异常

```java
  try { 
      result = field.get(obj);           
  } catch (IllegalArgumentException | IllegalAccessException e) {          
       e.printStackTrace();           
   }
```

* 泛型实例化类型自动推断

```java
List<String> list = new ArrayList<String>();
//java 7
List<String> list = new ArrayList<>();
```

## java8



