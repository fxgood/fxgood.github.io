---
title: Java知识点复习
category: Java
excerpt: 对java的知识点进行复习,主要包括java基础,容器,并发编程,jvm,新特性等.
---

# Java基础

## 基本概念

![image-20220613141652944](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220613141652944.png)

1. JDK,JRE和JVM
   - JDK>JRE>JVM
   - jdk包括jre,编译器javac,工具(如javadoc)等,能够创建和编译程序
   - jre包括jvm,标准类库等,是java运行时环境
   - jvm是java运行字节码的虚拟
     - 现在最常用的JVM规范的实现是:HotSpot VM
2. 什么是字节码?采用字节码的好处是什么?
   - 字节码是面向JVM的代码,既JVM可以理解的代码
   - 采用字节码的好处是: 跨平台行,一次编译,随处运行
   - 由于解释器逐行解释速度比较慢,后面引进了JIT编译器(just-in-time即时编译器),JIT在运行时编译.对于重复的字节码,jit可以将上次编译好的机器码直接拿过来用,加快了运行效率.
3. Java是编译型语言,还是解释型语言?
   - Java是编译和解释并存的语言. 
   - .java文件通过javac编译成.class文件,是编译过程
   - .class文件加载到jvm中会解释执行(解释成可以直接执行的机器码),这是解释过程
4. Java和C++的区别
   - C++是编译型语言,Java是编译和解释并存的语言
   - Java有垃圾回收机制GC
   - Java没有指针
   - Java的类是单继承,但是可以通过接口实现多继承
   - java不支持运算符重载
5. 权限修饰符?
   - public ,任何类中都能访问;修饰类的话,java源文件必须与public类名相同
   - protected,同一包和不同包的子类可以访问
   - default,无需修饰符,同一包下可以访问
   - private,只能在当前类中被访问
6. final, finally,finalize区别
   - final
     - 用在类上:不能继承
     - 用在方法上,不能初始化
     - 用在变量上:常量,不能修改,必须赋初值(直接赋值,构造函数,静态代码块,ps:这些都是在类的加载机制的初始化阶段对其进行赋值)
       - 基本数据类型:值不能改变
       - 引用数据类型:地址不能修改
   - finally: try-catch-finally,里边的代码无论发生什么都一定会执行
   - finalize: 是Object中的方法,也就意味着每个对象中都可以调用. 通知垃圾回收器回收此对象.但是不能保证会被回收.
7. 面向对象和面向过程?
   - 面向过程性能比较高,因为没有实例化等过程
   - 面向对象能实现高内聚,低耦合的代码设计,使得程序易维护,易扩展
8. 面向对象的三大特征?
   - 封装: 将数据和方法封装到类中,实现高内聚低耦合的设计
   - 继承:子类继承父类的成员变量和方法,并在此基础上进行拓展,提高了代码的复用性.
   - 多态:父类引用指向子类对象. 多态又叫延迟方法,指的是子类对象的类型在编译时无法确定,只有在运行时才能确定.

## 基本语法

1. 标识符和关键字的区别?

   - 标识符是类,变量,方法等的名字
   - 关键字是被赋予特殊含义的标识符
2. java语言的关键字有哪些

   - 访问控制: public private protected
   - 数据类型: byte short int long float double boolean char
   - 类,方法和变量修饰符: class interface enum abstract extends implements  
   - 错误处理: try catch finally throw throws
3. 静态方法为什么不能调用非静态成员?

   - 静态方法属于类,在类加载的时候就会分配内存
   - 非静态方法属于实例对象,只有对象实例化之后才会存在
   - 因此不能在内存中不存在非静态成员的时候调用它.
4. 重载和重写的区别?

   - 重载: 同名方法,参数列表不同
   - 重写:覆盖父类的相同方法
     - 方法重写要遵循"两同两小一大"原则: 即名称,参数列表相同; 返回值类型和异常类型应该比父类小或相同;访问权限应该比父类大或相同
5. 可变长参数?

   - 只能放在参数列表最右边

   - 编译后实际会被转换为一个数组
   - 在方法重载时不会被优先匹配
6. 基本类型和包装类型的区别?

   - 基本数据类型有默认值,而包装类型是对象类型,不赋值的话是null
   - 基本数据类型不能作为泛型
   - 包装类型在堆上分配内存,而基本数据类型**的局部变量**存放在java虚拟机栈的局部变量表中.(非静态成员变量如果用基本数据类型,那么会存放在堆中)
7. 包装类型的缓存机制了解吗?
8. Java的方法参数只有值传递,也是就方法得到的是所有参数值的一个副本

## String

特点

- 不可变
  - 原因是底层`byte[]value`数组用final和private修饰了,既不能改变指向,也不能访问而修改
  - 好处在于易于共享、可以避免多线程并发同时更新带来的麻烦(因为不可变,因此可以放心地让多个线程共享)
- 字面量都放在方法区的字符串常量池中(线程共享),同样的字符串只会保留一份
  - 因为实际开发中字符串使用的太频繁了,为了提高执行效率,所以都放在字符串常量池中

要点

- "abc"+"def"  执行这句会在字符串常量池中创建三个字符串
- `String s1=new String("abc"); String s2=new String("abc");` 内存中一共有三个对象. 堆中有两个String类型对象,都指向方法区字符串常量池的字面量"abc".

- String、StringBuilder、StringBuffer的区别
  - String底层字节数组不可变，另外两个可变
  - StringBuilder线程不安全，执行速度快
  - StringBuffer线程安全，执行速度慢，内部方法加了`synchronized`
- 为什么jdk9将底层的`char[]`改成了`byte[]`
  - 因为更加节省空间
    - 纯英文的字符串可以用`Latin-1`编码,一个字符占一个字节,如String name="jack",占4个字节
    - 带有中文的字符串 String name="人山人海",采用utf-16,就要占2*4=8个字节 (String源码中用coder来区分编码方式)

## synchronized关键字

## 对象析构与finalize方法

- java有垃圾回收机制,不需要人工回收内存
- java不支持析构器
- finalize可以通知gc进行垃圾进行,但是不能保证立即被清理

## C++类的大小

这篇文章写的很详细,[不过最后一个案例感觉有点问题](https://blog.csdn.net/Function_Dou/article/details/86592472)

- 空类1字节(编译器会用char占一个字节,C++中char是一个字节的,java是两个字节,utf-16编码)
- 32位系统指针大小为4字节,64位指针大小为8字节(CPU通用寄存器的大小,64位寻址空间为2^64字节)
- 存在虚指针时,要添加指向虚表的地址,因此多一个指针的大小(32位4字节,64位8字节),并且一定在类的最前边,因此继承的时候也要放在内存位置最前边
- 多个虚指针,也只会占用一个指针大小的内存,因为只需要一个指向虚表的指针
- 内存对齐



```c++

class T{ }	//占用一个字节  编译器会用char占一个字节 继承的时候会抹掉
class A
{
    int i, j;
}; //8字节    i i i i j j j j 
class B
{
    char ch;
    int i;
};//8字节    ch _ _ _ i i i i 
class C
{
    char ch1, ch2;
    int i;
};//8字节     ch1 ch2 _ _ i i i i 
class D
{
    char ch1;
    int i;
    char ch2;
};//12字节      ch1 _ _ _ i i i i ch2 _ _ _ 
//注意,这里i不能再往前了,int变量的起始位置必须是4的倍数
```

有vptr的类, 会在最前端存放一个指向虚表的指针(不论内部有多少个虚函数,都只有一个这样的指针)

```cpp
class A
{
    virtual void fun() {}
    int i, j;
};	// 16字节
class B
{
    virtual void fun() {}
    char ch;
    int i;	
};	// 16字节
class C
{
    virtual void fun() {}
    char ch1, ch2;
    int i;
};	// 16字节
class D
{
    virtual void fun() {}
    char ch1;
    int i;
    char ch2;
};	// 24字节

```

带有继承的类

```cpp
class A {};	//1字节
class B : A {}; // 1字节

class A
{
	char i;
};
class B : A
{
	char ch;
	int i;
};	// 8字节    A.i ch _ _ i i i i 

```

有虚指针的基类

```cpp
class A
{
	virtual void fun() {}
    virtual void f2(){}
	char i;
};	// 16字节
class B : A
{
	char ch;
	int i;
};	// 16字节  vptr vptr vptr vptr vptr vptr vptr vptr i ch _ _ i i i i 
class C : A
{
	int i;
	char ch;
};	// 24字节

```

```cpp
class A{
    virtual void f();
    virtual void f3();
    virtual void f5();
    virtual void f6();
};//8字节
class B:A{
    virtual  void f2();
    virtual  void f4();
    virtual  void f3();
};//8字节
```



有虚指针的子类

```cpp
class A
{
	virtual void fun() {}
	char i;
};	// 16字节
class B : A
{
	virtual void fun() {}
	char ch;
	int i;
};	// 24字节

```



# 集合

## ArrayList

底层结构:

- 底层是Object数组
- 如果不指定初始化长度,则先创建一个长度为0的数组,在第一次add的时候创建一个长度为10的Object数组(在源码grow方法中可以看到)
- 如果容量不够,会扩大1.5倍,底层用的是Arrays.copyOf(xx,xx)复制数组;

特点:

- 由于是顺序结构,所以具有顺序结构所有的优缺点
  - 优点:
    - 支持随机访问
    - 查询效率高
  - 缺点:
    - 扩容的话,涉及到数组的复制,效率非常低
      - 增删效率低
- 线程不安全
  - 如何变成线程安全的? 答:使用Collections.synchronizedList(List list)方法返回一个线程安全的List

## LinkedList

底层结构: 双向链表

特点:

- 实现了List和Deque双端队列接口

- 由于底层是双向链表,所以具有链表结构的所有优缺点
  - 优点
    - 增删效率高
    - 不需要连续的存储空间
  - 缺点:
    - 查询效率低,每次需要从头节点开始遍历找到目标节点
- 线程不安全
  - 如何变成线程安全的? 答:使用Collections.synchronizedList(List list)方法返回一个线程安全的List

## Vector

底层: 数组

特点:

- 与ArrayList最大区别就是线程同步(线程安全,其方法都是带有synchronized关键字)
- 每次扩容是原来的两倍

## Set

HashSet内部存了一个HashMap

TreeSet内部存了一个TreeMap

## HashMap

- 数据结构中哈希有哪些方法
  - 除留余数法  `%`
  - 直接定址法  $H(key)=key$或$H(key)=a*key+b$
    - 不会产生冲突
    - 适合关键字分布基本连续的情况
  - 数字分析法
    - 如手机号码作为关键字设计散列函数,那么前面几位基本上都是138,139.所以可以用后面4位作为三列地址
    - 要取分布比较均匀的几位数字
    - 缺点是需要知道关键字集合的特点,如果更换了关键字集合则必须重新设计散列函数
  - 平方取中法: 取关键字平方值的中间几位作为三列地址
    - 优点: 与各位数字都相关,因此散列地址比较均匀
    - 适用于关键字的每一位取值都不均匀的情况
- 什么是`装填因子`
  - 装填因子=表中记录数/散列表长度   HashMap中使0.75
  - 会直接影响查找效率
- 有哪些解决hash冲突的方法
  - 开放定址法(用空位来解决冲突) $H_i=(H(key)+d_i)\%m$,其中di增量有不同的取法
    - 线性探测法: 越早遇到空位置,就可以越早确定查找失败
    - 平方探测法
    - 伪随机序列法
  - 拉链法(还可以用红黑树)
  - 再散列法,准备多个散列函数
- get方法原理
  - 先计算key的hash值(调用key的hashCode方法),然后和底层数组长度-1相与计算出下标
  - 在该下标上用equals方法搜索是否有相同元素,如果有返回value,没有返回null
  - 注意没有该key和key的值是null都会返回null,需要用containsKey来区别
    - 线程不安全
- put方法原理
  - 先调用key的hashCode方法计算出hash,然后和数组长度-1进行相与,计算出实际下标
  - 然后和该下标对应的链表或红黑树进行一个一个元素比对(equals),如果有相同的,则更新value
  - 如果没有则接在末尾
- jdk8的数据结构变化
  - 链表元素超过8个转为红黑树
  - 红黑树元素少于6个转为链表
- 放进HashMap的元素需要同时重写hashCode和equals方法
  - hashCode方法原则: hash值不同的元素一定不同(hash值相同的元素,不一定相同)
  - hashCode散列应该比较均匀
- 可以存null
  - key可以为null,只能有一个
  - value可以为null,但是去的时候注意要调用containsKey来区分是不存在key还是value是null

问题:

1. 为什么hashmap允许key和value为null(但只能有一个),但是hashtable均不允许?

源码阅读

- 扩容过程
- 单链表和红黑树的转换过程 数据结构(红黑树节点继承Node,保持原来的顺序)

## Hashtable 

线程安全,不可以存null

## Properties

键值对都是String类型

常用方法

- String getProperties(String key)
- Object setProperties(String key,String value)

## TreeMap

特点

- 无序(插入和读取顺序不能保证相同)
- 可以排序
- key不能重复
- 底层数据结构是: 红黑树(自平衡二叉查找树)
- key必须提供排序规则
  - 自身实现Comparable接口,重写compareTo方法
  - 为TreeMap构造器提供一个比较器,重写compare方法





# 面向对象

## 基础

- 构造函数,不写默认有一个空的;写的话默认的就没有了

- 子类构造器
  - super(...) 调用父类构造器
  - 必须在子类构造器的第一行;如果不写,会默认调用父类空构造函数;
  - 如果父类没有空构造函数,子类也不明确指定父类有参构造器,则会报错
- 运行时确定方法和实例对象,称为动态绑定
- java不支持多继承,但是可以通过接口来实现
  - 接口可以多继承!
- final关键字
  - 修饰类:不可继承
  - 修饰方法:不可重写
  - 修饰变量:值不能被重新覆盖
    - 基本数据类型:值不可变
    - 引用数据类型:指向不可变
  - 修饰类或方法的原因: 确保他们在子类中不会改变语义
- 类型转换
  - 向上转型: 自动类型转换
  - 向下转型:强制类型转换
  - 无论向上还是向下类型转换,类之间本身必须要有继承关系,否则编译器会报错`ClassCastException`,即只能在继承层次内进行类型转换

## 抽象类

- abstract修饰类
  - 这个类是抽象类
  - 包含`>=0`个抽象方法
  - 不能实例化
  - 包含抽象方法的类一定是抽象类;但抽象类不一定包含抽象方法
  - 如果子类没有实现抽象父类的全部方法,那么仍然是抽象类
- abstract修饰方法
  - 这个方法是抽象方法,没有方法体

## 使用预定义类

LocalDate类

三种静态工厂方法来创建一个LocalDate类型对象(构造器私有,无法直接通过new来构造)

```java
LocalDate.of(2020,4,20);
LocalDate.now();
LocalDate.parse("2021-04-20");
```

## 浅拷贝与深拷贝

## Java内存模型JMM

```
我们常说的JVM内存模式指的是JVM的内存分区；而Java内存模式是一种虚拟机规范。

Java虚拟机规范中定义了Java内存模型（Java Memory Model，JMM），用于屏蔽掉各种硬件和操作系统的内存访问差异，以实现让Java程序在各种平台下都能达到一致的并发效果，JMM规范了Java虚拟机与计算机内存是如何协同工作的：规定了一个线程如何和何时可以看到由其他线程修改过后的共享变量的值，以及在必须时如何同步的访问共享变量。
```



- 进程/线程通信的的方法有
  - 共享变量(Java采用)
  - 消息通信

这个回头再看,感觉面试不怎么问吧!

## 对象的内存布局

对象实例包括三部分

- 对象头
  - 对象运行时元数据: hash值,gc分代年龄,各种锁的状态(锁状态标志,线程持有的锁,偏向进程id,偏向时间戳)
  - 类型指针:指向方法区中的类型数据
  - 如果是数组类型,还包括数组的长度
- 实例数据
  - 真正存储有效信息的部分,包括程序中定义的各种字段,也包括从父类,Object类中定义的各种字段
- 对齐填充
  - 虚拟机要求对象的起始地址必须是8字节的整数倍(对象头刚好是8字节)
  - 因此不够8字节的话,要填充至8字节



![image-20220707213530875](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220707213530875.png)

## 对象的访问定位

堆中的对象是通过元数据指针找到方法区中的元数据信息的,那么栈帧中的对象引用又是如何找到对应的对象呢? 两种方法:

方法1:通过句柄

堆区中开辟了⼀块空间⽤于存储句柄，该空间称为句柄池。句柄池分别存储着两个句柄，分别是到对象实例数据的指针和到对象类型数据的指针。前者指向对象实例数据，后者指向对象类型数据

![image-20220707213844187](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220707213844187.png)

方法2:直接访问(HotSpot虚拟机中的访问方式)

![image-20220707213857715](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220707213857715.png)

## 锁

对象先了解有哪些锁,有哪些特征即可,具体实现先不了解

## Java运行时数据区(内存布局)

![img](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/9f510fb30f2442a7bffb0739db0a044cd0130201.jpeg)





# Java的集合体系

略

看了一遍,感觉没什么好记录的 

# 接口

## 接口基础知识

- Java的接口可以多继承,类也可以实现多个接口

- 所有成员都是自带public的,可以省略
  - 方法的public abstract省略
  - 成员变量的public static final 可以省略(接口不能有实例字段,即不加static的字段)
- java8接口中可以有静态方法
  - 必须有方法体
  - 子类不用实现它
- java8接口中可以有默认方法
  - 相当于给原来的接口方法加了一个默认实现,其他所有东西都不变
  - 提供默认方法的原因是: java8之前,如果想给一个接口添加一个抽象方法,那么所有的实现类必须要实现这个方法,即使用不到也得写一个空实现,需要修改所有实现类,很麻烦; 加了默认方法之后就解决了这个问题.
- java9接口中可以有私有方法
  - 给其他方法打辅助的,给接口中其他方法调用的
- 可以用instance of来判断某个对象是否实现了某接口

## 常用接口相关知识

- 实现Comparable接口时,重写compareTo方法,其实现要与equals逻辑上兼容.
- Cloneable接口
  - 它是一个标识接口,内部没有具体成员;如果调用一个对象的clone方法,而它没有implements Cloneable接口,则会抛出异常

# Lambda表达式

- 如果可以推导出类型,则不用写类型
- 如果只有一个参数,那么可以省略小括号
- 如果只有一句return,那么可以省略中括号和return

```java
Integer[]arr3=null;
Arrays.sort(arr3,(a,b)->a-b);
```

- **Lambda表达式中只能是引用数据类型,所以对`int[]`不能直接实现反向排序**

## 函数式接口

1. 只有一个抽象方法的接口,称之为函数式接口,**可以提供一个lambda表达式来实现它**.
2. lambda表达式就是函数是接口的一个实例
2. `@FunctionalInterface`用来标记函数式接口（标记注解，没有实际内容），即只有一个抽象方法（可以有其他多个静态方法，默认方法）

## 四种基本的函数式接口

- predicate：泛型单入参返回布尔值出参
- function：泛型单入参返回泛型出参
- supplier：无入参返回泛型出参
- consumer：泛型单入参无出参

均可以用lamba表达式来创建对应类型的实例

## 方法引用

方法引用就是用你提供的方法来代替函数式接口中的抽象方法

不能独立存在，总是转换为函数式接口的实例

下面的案例中有三种写法

1. 匿名内部类实现断定型函数式接口的test方阿飞
2. lambda表达式实现test方法
3. 提供一个方法引用来替换test方法（形式一致）

```java
public class Tesy {
    public static void main(String[] args) {
        new Tesy().f();
    }
    void f(){
        List<String>s=Arrays.asList("abc","def","g","k","iLoveYou");
        List<String> filter = filter(s, new Predicate<String>() {
            @Override
            public boolean test(String s) {
                return s!=null && s.length()>2;
            }
        });
//        List<String> filter = filter(s, e -> e != null && e.length() > 2);
        //List<String> filter = filter(s, Tesy::strategy);
        System.out.println(filter);
    }

    static boolean strategy(String s){
        return s!=null && s.length()>2;
    }

    //根据给定策略过滤字符串
    List<String> filter(List<String>list, Predicate<String>predicate){
        List<String>ans=new ArrayList<>();
        for(String s:list)
            if(predicate.test(s))
                ans.add(s);
        return ans;
    }
}
```

## 构造器引用与数组引用

```java
Supplier<Stu>supplier=()->new Stu();
Supplier<Stu>supplier2=Stu::new;
Supplier<Stu>supplier3=new Supplier<Stu>() {
    @Override
    public Stu get() {
        return new Stu();
    }
};
```

```java
class Stu {
    int id;
    Stu(){}
    Stu(int id){this.id=id;}
}


Function<Integer,Stu>function=id->new Stu(id);
//Function<Integer,Stu>function= Stu::new;
Stu stu = function.apply(10);
```





# 类加载机制

## 类加载机制

## 类的生命周期

加载

链接

- 验证
- 准备
- 解析

初始化

使用

卸载

# 重要概念

## 浅拷贝与深拷贝

- 引用拷贝就是两个变量指向同一个对象;

- 对象拷贝是复制一个对象,地址不同;对象拷贝又分为浅拷贝和深拷贝

  - 浅拷贝:对所有成员变量进行简单复制值,对基本数据类型来说没问题,但是对引用数据类型来说,仍然是只复制了引用对象的地址.

  - 深拷贝:对引用数据类型也进行克隆.

- java Cloneable接口
  - 是标记接口,内部是空的; clone方法是从Object中继承的,访问权限是protected,也就意味着如果子类不重写成public的,那么在类的外部无法被访问.(protected权限:同一个类中,包内,(含包外)子类中)
  - 如果不实现它的话,调用对象的clone方法,会报异常
- 所有数组类型都会有一个public的clone方法,可以用这个方法简历一个新的数组,包含原数组的所有副本(浅拷贝)

