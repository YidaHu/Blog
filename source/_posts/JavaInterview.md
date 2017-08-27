---
title: Java面试整理
date: 2017-03-28 23:00:36
categories: Java
tags:
- 面试
- 数据结构
- 算法
- 数据库
- JavaEE
---

![work](JavaInterview\work.jpg)

# 前言

这篇文章里面总结了很多Java相关的知识，基本上应该算是每个Java程序员必须会的一些知识，所以，也就是很多面试官喜欢拿来考的一些东西。总结他们第一个目的是自己能够经常拿出来看一看，第二个也是可以给别人一些借鉴。

**为了方便，我把他们分了类，有一些是必看的，我用！标注，有一些进阶型的我用%标注，有一些需要了解的，我用？标注。**

<!--MORE-->

此文转载而来，用来好好研究学习一下。文章来源http://www.hollischuang.com/archives/10 ，感谢此博主的用心整理。

1. **计算机基础知识**
2. **数据库相关**
3. **C语言基础**
4. **Java基础**
5. **Java高级**
6. **J2EE相关**
7. **面向对象**
8. **思维清晰水平考察**
9. **推荐阅读**

## 必会关键字

### `void` `byte` `int` `long` `char` `short` `float` `double` `String` `StringBuffer` `StringBuilder``Array` `Collection` `Collections` `List` `ArrayList` `LinkedList` `Vector` `Set` `HashMap``TreeMap` `LinkedHashMap` `ConcerrentHashMap` `Set` `TreeMap` `HashMap` `synchronized``volatile` `transient` `implements` `extends` `public` `private` `protected` `this` `super``static` `final` `const` `null` `run` `start` `thread` `enmu` `quicksort` `mergesort` `heapsort``bubblesort` `selectsort` `insertsort` `stack` `queue` `list` `heap` `tree` `avlTree` `Btree``B+Tree` `RTree` `throw` `throws` `try` `catch` `finally` `break` `continue` `instanceof`

## 计算机基础知识

### 数据结构

> %1、队列、栈、链表、[树](http://blog.csdn.net/zhangerqing/article/details/8822476)、堆、图
> ！2、栈和队列的相同和不同之处
> ？3、栈通常采用的两种存储结构
> ！4、`ArrayList`,`Vector`, `LinkedList`的存储性能和特性
> %5、各种树(`平衡树`,`排序树`,`B树`,`B+树`,`R树`,`多路树`,`红黑树`)

### 算法

> ？1、实现链表排序的一种算法。说明为什么你会选择用这样的方法？
> ！2、`排序`都有哪几种方法？请列举。
> ！3、各种排序算法的`时间复杂度`和`稳定性`
> %4、字符串(单链表)逆序
> ！5、`深度优先搜索`和`广度优先搜索`
> %6、使用栈实现链表/使用链表实现栈
> %7、全排列、贪心算法、KMP算法、hash算法、海量数据处理

### 操作系统

> ？1、虚拟内存管理
> ？2、换页算法
> ？3、进程间通信

### LINUX相关命令及操作

> %1、Linux 一些基本命令,如看load，查看文件内容
> %2、列出几个比较常见的命令，并解释下命令的用法

### 计算机网络

> ！1、tcp,udp区别
> ！2、HTTP请求和响应的全过程
> ！3、`osi`七层模型以及`tcp/ip`四层模型(每一层主要功能,传输的内容,主要协议,主要应用)
> ！4、三次握手，四次关闭，丢包,粘包，容量控制，拥塞控制
> ？5、子网划分

------

## 数据库相关

### 关系模型理论：

> ！1、`范式`
> ？2、`rownum`和`rowid`的区别与使用

### 事务相关

> %1、`Transaction`有哪几种隔离级别？（Isolation Level）
> ？2、`Global transaction`的原理是什么?
> ！3、`事务`是什么？

### 并发控制

> %1、`乐观锁`，`悲观锁`

### ORACLE或MYSQL题目

> ！1、`分页`如何实现（`Oracle`，`MySql`）
> ！2、Mysql引擎

### 其它

> %1、数据库操作的性能瓶颈通常在哪里, 1000万级别访问，在数据库和java程序上考虑哪些来进行性能优化
> %2、性能方面。多数结合多线程、同步来问，以提取一张大表数据来作为例子 解决性能的方法
> ！3、表关联时，`内连接`，`左连接`，`右连接`怎么理解？
> ！4、`Statement`和`PreparedStatement`之间的区别
> ！5、用JDBC怎样从数据库中查询一条记录
> %6、`索引`以及`索引的实现`(B+树介绍、和B树、R树区别

------

## C语言基础

### 构造函数、析构函数

> %1、为什么不要在构造器中调用虚函数
> %2、为什么不要在析构函数中抛出异常

### c++相关

> ！1、面向对象的三大基本特征，五大基本原则
> %2、C++继承的内存布局
> %3、C++多态的实现机制
> ！4、new、delete、malloc、free

### 其他

> ！1、为什么使用补码
> %2、C语言中的内存泄漏
> ！3、进制转换
> ！4、自己编写strlen/strcpy/strcmp

------

## 一、Java基础

### 继承、抽象类与接口区别、访问控制(private, public, protected，默认)、多态相关

> ！1、`interface`和 `abstrat class`的区别
> ！2、是否可以继承多个接口，是否可以继承多个抽象类
> %3、`Static Nested Class` 和 `Inner Class`的不同
> ！4、`Overload`和`Override`的区别。`Overloaded`的方法是否可以改变返回值的类型?
> ！5、`abstract`的method是否可同时是`static`,是否可同时是`native`，是否可同时是`synchronized`
> ！6、是否可以继承`String`类
> ！7、构造器`Constructor`是否可被`override`?
> ！8、作用域`public`,`protected`,`private`,以及`不写`时的区别?

### collections相关的数据结构及API

> ！1、列举几个`Java Collection`类库中的常用类
> ！2、`List`、`Set`、`Map`是否都继承自`Collection`接口？
> ！3、`HashMap`和`Hashtable`的区别
> %4、`HashMap`中是否任何对象都可以做为key,用户自定义对象做为key有没有什么要求？
> ！5、`Collection` 和 `Collections`的区别
> %6、其他的集合类：`concurrenthashmap`,`treemap`,`treeset`,`linkedhashmap`等。

### 异常体系

> ！1、`Error`、`Exception`和`RuntimeException`的区别，作用又是什么？列举3个以上的`RuntimeException`
> ！2、Java中的异常处理机制的简单原理和应用
> ！3、内存溢出和内存泄露

### 其它

> ！1、`String`和`StringBuffer`、`StringBuilder`的区别
> ！2、String s = “123”;这个语句有几个对象产生
> ！3、`reader`和`inputstream`区别
> ！4、`==`和`equals`的区别
> %5、`hashCode`的作用
> %6、`hashCode`和`equals`方法的关系
> ？7、Object类中有哪些方法，列举3个以上（可以引导）
> ！8、`char`型变量中能不能存贮一个中文汉字?为什么?
> %9、了解过哪些JDK8的新特性，举例描述下相应的特性？
> ！10、`Input/OutputStream`和`Reader/Writer`有何区别？何为字符，何为字节？
> ！11、如何在字符流和字节流之间转换？
> ！12、启动一个线程是用`run()`还是`start()`?
> %13、海量数据查询、存储
> ！14、`switch`可以使用那些数据类型
> ！15、多线程与死锁
> %16、Java的四种引用
> ！17、序列化与反序列化
> ！18、自动装箱与拆箱
> ！19、正则表达式

### JAVA开发工具、环境的使用

> IDE、maven、svn/git、Linux、Firebug

------

## 二、 Java高级

### 多线程

> ！1、多线程的实现方式，有什么区别
> %2、`同步`和`并发`是如何解决的
> 3、什么叫`守护线程`，用什么方法实现守护线程（`Thread.setDeamon()`的含义）
> %4、如何停止一个线程？
> ！5、解释是一下什么是`线程安全`？举例说明一个线程不安全的例子。解释`Synchronized`关键字的作用。
> ！6、当一个线程进入一个对象的一个`synchronized`方法后，其它线程是否可进入此对象的其它方法?

### 内存结构，GC

> ！1、`gc`的概念，如果A和B对象循环引用，是否可以被GC？
> %2、`Java`中的内存溢出是如何造成的
> %3、jvm gc如何判断对象是否需要回收，有哪几种方式？
> ？4、Java中的`内存溢出`和C＋＋中的`内存溢出`，是一个概念吗？
> ！5、引用计数，对象引用遍历；jvm有哪几种`垃圾回收机制`？讲讲`分代回收机制`

### CLASSLOADER

> ！1、`ClassLoader`的功能和工作模式

### NIO

> ？1、`IO`和`NIO`本质不同在实际项目使用场景及如何使用

### 其它

> ？1、`hashcode` 有哪些算法
> %2、`反射`,是否可以调用私有方法,在框架中的运用
> ？3、知道`范型`的实现机制吗？
> ？4、`Socket编程`通常出现的异常有哪些，什么情况下会出现
> ？5、了解JVM启动参数吗？`-verbose -Xms -Xmx`的意思是什么？
> %6、`StringBuffer`的实现方式，容量如何扩充
> %7、`代理机制`的实现

------

## 三、J2EE相关

### Servlet的掌握，包括新的异步Servlet

> ！1、`Servelt`的概念。常问`http request`能获得的参数
> %2、servlet中，如何定制`session`的过期时间？
> ！3、Servlet中的`session`工作原理 （`禁用cookie如何使用session`）
> ！4、servlet中，`filter`的应用场景有哪些？
> ！5、描述JSP和Servlet的区别、共同点（JSP的工作原理）。
> ？6、JSP的动态`include`和静态`include`
> ！7、Servlet的生命周期

### WEB框架的掌握(挑其掌握的一种)

> ！1、`Struts`中请求的实现过程
> ！2、`MVC`概念
> %3、谈一下自己最熟悉的web框架？然后就了解的web框架再深入下去
> %4、`Spring mvc`与`Struts mvc`的区别 （什么是Mvc框架）
> ？5、`Service`嵌套事务处理，如何回滚

### http相关(内部重定向，外部重定向)，http返回码

> ！1、`session`和`cookie`的区别
> ！2、HTTP请求中`Session`实现原理？
> %3、如果客户端**禁止Cookie能实现Session吗**？
> ！4、http `get`和`post`区别
> ！5、在web开发中，用`redirect`与`forward`做跳转有什么区别？web应用服务器对用户请求通常返回一些状态码，请描述下分别以**4和5开头的状态码**

### spring，ibatis，hibernate相关

> ？1、`Hibernate`/`Ibatis`两者的区别
> ？2、`OR Mapping`的概念
> %3、`hibernate`**一级和二级缓存**是否知道
> ？4、使用`hibernate`实现集群部署，需要注意些什么
> ！5、Spring如何实现`AOP`和`IOC`的？
> ！6、Spring的核心理念是什么？是否了解IOC和AOP
> ！7、Spring的`事务管理` ，`Spring bean注入`的几种方式
> ！8、Spring `AOP`解决了什么问题

### jboss，tomcat等容器相关

> ？1、`Tomcat`和`weblogic`的最根本的区别
> ？2、`Jboss`端口在哪个配置文件中修改

### web安全，SQL注入，XSS, CSRF等

> %1、`SQL注入` SQL安全

### AJAX相关

> ？1、`AJAX`感受，有什么缺点？
> %2、你使用的是`Ajax`的那种框架？
> ？3、`Ajax`如何解决跨域问题

### Web services

> ？1、简述`WebService`是怎么实现的

### JMS

> ？1、`JMS`的模式两种模式

### 其它

> ？1、`Js:confirm()`方法
> ？2、`Iframe`的优缺点
> %3、我们在web应用开发过程中经常遇到输出某种编码的字符，如`iso8859-1`等，如何输出一个某种编码的字符串?（主要是考量有没有碰到过编码问题，问题是如何解决的）
> ？4、怎么获取到客户端的真实IP?
> ？5、名词解释：`jndi，rmi，jms，事务`，如果有了解的话可以深入
> ？6、WEB层如何实现`Cluster`

------

## 四、面向对象

### 高内聚，低耦合方面的理解

> ？1、在项目中是否采用分层的结构，是怎样划分的，各层之间采用了哪些相关技术？ 对哪些设计模式比较熟悉？
> %2、什么是`低耦合`和`高聚合`？`封装原则`又是什么意思？
> %3、类A依赖类B，会产生什么问题？怎样解除这种`耦合`？

### 设计模式方面

> %1、谈一下自己了解或者熟悉的`设计模式`
> ！2、`Singleton`的几种实现方式
> ？3、`工厂模式`和`抽象工厂模式`之间的区别
> ！4、简述`M-V-C`模式解决了什么问题？

### 其它

> %1、说说你所知道的`UML`图，在项目中是如何运用的

------

## 思维清晰水平考察

### 一、从基础知识里体现其思维清晰水平

参考问题

```
你知道设计模式吗？你用过哪些设计模式？在什么场合下用的？
你怎样保证你的代码可以处理各种错误事件？ 判断依据是一定要有自己的思考和分析以及总结
```

### 二、多角度思考问题、系统而全面地分析各种事件，一定要有自己的判断 比如项目中用到哪些技术，并分析各种技术的优缺点，一定要有自己的思考和判断

### 三、针对项目情况，顺藤摸瓜，考察其项目的一些沉淀及思考。

```
简述一个你最有成就的项目（包括团队，自己在团队中的角色）
有没有比较棘手的问题
如何发现的问题（是否找问题的根源）
问题是否已经解决，是如何解决的
    如果已解决，是否是你自己通过努力解决的，做了什么样的努力
    如果未解决，原因是什么，你觉得怎么样可以比较好的解决掉这个问题
描述完毕以后针对未描述点询问，是否考虑的性能问题，是否考虑部署结构，有没有比较得意的设计之处
项目过程中的文档情况，你觉得那些还有那些文档没有建立，是有必要建立的。
作为负责人如何保证项目的质量，有了那些措施
```

### 四、出题，考验其思维推导的能力 例如：

```
估计一下杭州有多少软件工程师，如果允许，你还需要那些调研工作？并给出你的推导过程。
估算下淘宝的商品数，给出推导过程
如果让你做一个网站，如何估算网站的最大并发数
```