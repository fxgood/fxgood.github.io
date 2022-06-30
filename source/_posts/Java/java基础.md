---
date: 2020-07-11
title: Java基础
category: Java
tag: 编程语言
excerpt: 记录javase的基础知识
---



# 1. 概述

- 冯诺依曼体系结构
	- 组成：运算器，控制器，存储器，输入设备，输出设备
- SUN公司1995年推出的一门高级编程语言
- JDK：Java Development Kit （java开发工具包）
	- 包括jre
	- 其他开发工具：编译工具javac.exe，打包工具jar.exe
- JRE：Java Runtime Environmen（java运行时环境 ）
	- 包括Java虚拟机（Java Virtual Machine）和Java程序所需要的核心类库等。
	- 如果想**运行**一个开发好的java程序，计算机中只需要安装JRE即可。 
	![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210711133429669.png)
	![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210711133540799.png)
- Java内存模型
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210714202358430.png)

# 2. 基本语法
## 2.1 关键字和标识符
### 关键字
- 关键字的定义和特点
	- 定义：被Java语言赋予了特殊含义的字符串
	- 特点：都是小写
- 用于定义数据类型的关键字
byte,short,int,long,float,double,boolean,void,class,interface,enum
- 用于流程控制的关键字
if,else,switch,for,while,case,default,do,break,continue,return
- 用于定义访问权限修饰符的关键字
public,protected,private
- 定义类，函数，变量修饰符的关键字
abstract,final,static,synchronized
- 用于定义类与类之u见关系的关键字
extends,implements
- 用于定义简历实例及引用实例，判断实例的关键字
new,this,super,instanceof
- 用于异常处理的关键词
try,catch,finally,throw,throws
- 用于包的关键字
package,import
- 其他修饰符关键字
native,strictfp,transient,volatile,assert 

### 保留字
现有Java版本尚未使用，但以后版本可能会作为关键字使用，如goto，const
### 标识符命名规范
1. 包名：全部小写,`xxxyyyzzz`
2. 类名、接口名：大驼峰
3. 变量名、方法名：小驼峰
4. 常量名：全部大写，多个单词用下划线连接

## 2.2 变量
### 基本数据类型
- Java语言数据类型的长度和表示范围不受OS影响
- 基本数据类型(primitive type)
	- 数值型
		- 整数类型 
			- byte:1字节，范围：-128-127
			- short:2字节，范围:$-2^{15}$ ~ $2^{15}+1$ 
			- int:4字节，范围$-2^{31}$ ~ $2^{31}-1$（约21亿）
			- long:8字节，范围$-2^{63}$ ~ $2^{63}-1$
			声明long类型常量须加小写`l`或者大写`L` 
		- 浮点类型
			- 单精度float，占4字节，尾数可以精确到**7位有效数字**
				- **float类型表示范围比long还大！** 因为float类型底层存储方式不同，$2^E*M$
				- 浮点数关注的是精度，整数类型关心的是范围，。
			- 双精度double，占8字节，精度是float的两倍。通常采用此类型
			- java的浮点型常量默认为double类型，声明float类型，须后加小写`f`或者大写`F`
			- 两种表示形式
				- 十进制数形式：2.12, 512.0f, .34
				- 科学计数法形式：5.12e2, 512E2, 100E-2 
	- 字符型
		char，占2个字节
		- 三种表示方式
			- 单引号
			- 转义字符
			\r回车符，\n换行符
			- Unicode值直接表示，`\uXXXX`，XXXX代表一个十六进制整数
		- char类型可以运算，因为有对应的Unicode码  
		- UTF-8是使用最广的一种Unicode的实现方式
	- 布尔型
		boolean
- 引用数据类型(reference type)
	- 类(包括String）
	- 接口
	- 数组  
### 基本数据类型之间的转换
除了boolean类型，其他7种基本数据类型之间的转换
### 自动类型提升
- 当容量（表示数的范围）小的数据类型的变量与容量大的数据类型的变量之间做运算时，结果自动提升为容量大的数据类型。
	- 如果用`int=int+long`则会报错 
- 容量（表示数的范围）从小到大是：byte（1字节）-> char、short （2字节）-> int -> long -> float -> double
	(**注意，float虽然是4个字节，但是比8个字节的long，能表示的数据范围要更大**）
	- **特别地，byte、char、short三种类型的变量之间做运算时（包括自己和自己，如byte+byte），结果为int类型**
	- 书上：java在做运算地时候，如果操作数均在int范围内，那么一律在int的空间内运算
### 强制类型转换
1. 需要使用强转符号`()`
2. 可能造成精度损失
3. 不加`l`或者`L`的整数，默认是int型
	- 比如 `long a=213214332143254324;`
	- 会报错，整数超过int类型
4. 不加`f`或者`F`，默认是double类型
	- 比如`float b=12.3;`  会报错
	- 因为12.3默认是double类型，不能直接赋值给精度小的float类型
### 常量
1. 整型常量，默认类型为int型
2. 浮点型常量，默认类型为double型
### String
- 声明String时，用双引号即可
- **不是基本数据类型，属于引用数据类型**
- 字符串可以拼接其他字符串，也可以拼接其他数据类型的数据
	- 可以与基本数据类型，直接做`+`运算 
### 进制转换
- 对于整数，有四种表示方式
	- 二进制binary，以`0b`或者`0B`开头
	- 十进制decimal
	- 八进制octal，以数字`0`开头
	- 十六进制hex，以`0x`或者`0X`开头 	
- 二进制的整数有如下三种形式
	- 原码：直接将一个数值转换成二进制。最高位是符号位
	- 反码：
		- 正数：和原码一样
		- 负数：除符号位外，其他位对原码按位取反
	- 补码：
		- 正数：和原码一样
		- 负数：反码+1  	 
		- -127补码是`1000 0001`，-128补码是`1000 0000`，用-127减一即可 
## 2.3 运算符
Java中的运算符有
- 算术运算符
- 赋值运算符
- 比较运算符（关系运算符）
- 逻辑运算符
- 位运算符
- 三元运算符
- 位运算符
`>>>`无符号右移

移位运算符和位运算符优先级如下
`~，<<，>>，>>>，&，^，|`

## 2.4 数组
对char型数组，System.out.println()打印的结果是内容的字符串形式
对于其他类型的数组，打印的结果是`类型@地址值`，如`[I@ed9d034`
# 3. 封装
面向对象的三大特征
- 封装
- 继承
- 多态
## 3.1 java内存模型
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210714202456278.png)
- 堆Heap
唯一目的是**存放对象实例**， 几乎所有的对象实例都在这里分配内存。在Java虚拟机规范中的描述是：所有的对象实例以及数组都要在堆上分配。
- 栈Stack
通常所说的**栈Stack**，指的是虚拟机栈。虚拟机栈用于存储**局部变量**等。局部变量表存放了编译期可知长度的各种基本数据类型(byte,short,char,int,long,boolean,float,double)、对象引用(reference类型，是对象在堆内存的首地址）。方法执行完，自动释放。
 **Java虚拟机栈也是线程私有的，它的生命周期与线程相同（随线程而生，随线程而灭）**， Java虚拟机栈描述的是Java方法执行的内存模型：每个方法执行的同时会创建一个栈帧。对于我们来说，主要关注的stack栈内存，就是虚拟机栈中局部变量表部分。
 <br>栈帧（Stack Frame）是用于支持虚拟机进行方法调用和方法执行的数据结构。它是虚拟机运行时数据区中的java虚拟机栈的栈元素。
栈帧存储了方法的局部变量表、操作数栈、动态连接和方法返回地址等信息。
每一个方法从调用开始至执行完成的过程，都对应着一个栈帧在虚拟机里面从入栈到出栈的过程。
- 方法区Method Area
用于存储已被虚拟机加载的**类信息、常量、静态变量、即时编译器编译后的代码**等数据。
(JDK8以后改成了元数据空间metaspace）
- 程序计数器
```
	程序计数器是一个记录着当前线程所执行的 字节码的行号指示器。
	JAVA代码编译后的字节码在未经过JIT（实时编译器）编译前，其执行方式是通过“字节码解释器”进行解释执行。简单的工作原理为解释器读取装载入内存的字节码，按照顺序读取字节码指令。读取一个指令后，将该指令“翻译”成固定的操作，并根据这些操作进行分支、循环、跳转等流程。
　从上面的描述中，可能会产生程序计数器是否是多余的疑问。因为沿着指令的顺序执行下去，即使是分支跳转这样的流程，跳转到指定的指令处按顺序继续执行是完全能够保证程序的执行顺序的。假设程序永远只有一个线程，这个疑问没有任何问题，也就是说并不需要程序计数器。但实际上程序是通过多个线程协同合作执行的。
　首先我们要搞清楚JVM的多线程实现方式。JVM的多线程是通过CPU时间片轮转（即线程轮流切换并分配处理器执行时间）算法来实现的。也就是说，某个线程在执行过程中可能会因为时间片耗尽而被挂起，而另一个线程获取到时间片开始执行。当被挂起的线程重新获取到时间片的时候，它要想从被挂起的地方继续执行，就必须知道它上次执行到哪个位置，在JVM中，通过程序计数器来记录某个线程的字节码执行位置。因此，程序计数器是具备线程隔离的特性，也就是说，**每个线程工作时都有属于自己的独立计数器。**
```
> 运行时数据区包括：方法区、虚拟机栈、本地方法栈、堆、程序计数器
## 3.2 对象
- 对象的内存解析
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210714204115273.png)
- 对象数组的内存解析
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210714205208417.png)

注意：
1. 此处的字符串“Tom”，是字符串常量，实际上是在方法区
2. 引用类型的变量，只可能存储两类值：null 或 地址值（包含变量的类型）
## 3.3 方法
- 方法重载
概念：在同一个类中，允许存在一个以上的同名方法，只要他们的**参数个数 或 参数类型**不同即可。
特点：与返回值类型无关，只看参数列表。
- 可变形参的方法
允许直接定义能和多个实参相匹配的形参。从而，可以使用一种更简单的方式，来传递个数可变(任意个，包括0个）的参数。
具体使用
1. 格式：`数据类型 ... 变量名`
2. 传入参数个数可以是：0个，1个，2个...
3. 可以与形参不同的方法之间形成重载
4. 与形参类型相同的数组，**不能共存**
5. 在形参列表中，**必须声明在末尾**
6. 形参列表中，**最多只能声明一个可变形参**
定义
```java
public void show(String ... strs){
	//可以像使用数组一样使用参数strs
	for(int i=0;i<str.length();i++){
		System.out.println(strs[i]);
	}
}
```
- 方法参数的值传递机制
Java里的方法的参数传递方式只有一种：**值传递**。即将实际参数值的副本传入方法内，而参数本身不受影响。
- 形参是基本数据类型：将其**数据值**传递给形参
- 形参是引用数据类型：将其**地址值**传递给形参
***
关于字符串作为形参的情况
```java
public class p1 {
    static void change(String s2) {
        s2 = "bbb";
    }

    public static void main(String[] args) {
        String s1 = "aaa";
        change(s1);
        System.out.println(s1);
    }
}
//打印结果：aaa
```
Java中的String对象具有不可改变性，这里java在常量池中开辟了一块区域给`aaa`，s1指向`aaa`。change函数的形参仍然是地址传递（String是引用数据类型），s2也指向`aaa`，然后`s2="bbb"`即将s2指向`bbb`，s1的指向并未改变。因此最终打印的结果仍然是`aaa`
## 3.4 四种权限修饰符
封装性的体现：
1. 我们将类的属性xxx私有化，同时，提供公共方法来获取和设置它。
2. 不对外暴露的私有的方法
3. 单例模式
4. ...

我们设计程序追求“高内聚、低耦合”
- 高内聚：类的内部数据操作细节自己完成，不允许外部干涉
- 低耦合：仅对外暴露少量的方法用于使用
***
Java规定了4种权限：private, (default), protected, public 
| 修饰符    | 类内部 | 同一个包 | 不同包的子类 | 同一个工程 |
| --------- | ------ | -------- | ------------ | ---------- |
| private   | Yes    |          |              |            |
| (default) | Yes    | Yes      |              |            |
| protected | Yes    | Yes      | Yes          |            |
| public    | Yes    | Yes      | Yes          | Yes        |


四种权限可以用来修饰类以及类的内部结构：属性、方法、构造器、内部类
- 对于class的权限修饰，只可以用public和default（缺省）
	- public类可以在任意地方被访问
	- default类只可以被同一个包内部的类访问

## 3.5 构造方法
说明
1. 如果没有显示地定义类的构造器的话，系统默认会提供一个空参的构造器；**一旦我们显示定义了类的构造器之后，系统就不再提供默认的空参构造器。**
2. 定义构造器的格式：`权限修饰符 类名(形参列表){...}`
3. 构造器可以重载

## 3.6 JavaBean
说明
1. JavaBean是一种Java语言写成的可重用组件
2. 所谓JavaBean，是指符合以下标准的Java类
	- 类是公共的
	- 有一个无参的公共构造器
	- 有属性，且有对应的get和set方法
## 3.7 UML类图
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210714220031973.png)
## 3.8 this
说明
1. 它在方法内部使用，即这个方法所属对象的引用
2. 它在构造器内部使用，表示该构造器正在初始化的对象
3. 用于构造器调用其他构造器（必须声明在当前构造器的首行，且最多只能使用一次）
```java
public Person{
	public Person(){...}
	public Person(int age){
		this();
		...
	}
	public Person(int age,string name){
		this(age);
		...
	}	
}
```
## 3.9 JDK中主要的包介绍
1. `java.lang`：包含Java语言的核心类，如String、Math、Integer、System和Thread，提供常用功能
2. `java.net`：包含执行与网络相关操作的类和接口
3. `java.io`：包含能提供多种输入/输出功能的类
4. `java.util`：包含一些实用的工具类，如定义系统特性、接口的集合框架类、使用与日期日历相关的函数
5. `java.text`：包含了一些java格式化相关的类
6. `java.sql`：包含了java进行JDBC数据库编程的相关类/接口
7. `java.awt`：包含了构成抽象窗口工具及(abstract window toolkits）的多个类，用于构建和管理应用程序的GUI，现在不常用了。
## 3.10 MVC设计模式
MVC是常用的设计模式之一，将整个程序分为三个层次：视图、控制器、数据模型。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210716093728943.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjI1OTA4OA==,size_16,color_FFFFFF,t_70)
## 3.11 import关键字
说明
1. 可以使用`xxx.*`方式，导入xxx包下的所有结构（**子包除外，需要再显示声明**）
2. `java.lang`包或本包下定义的类和接口，可以省略import
3. 如果在源文件中，使用了两个不同包下的同名的类，则至少有一个要以全类名的方式书写（xxx.xxx.类名)
4. `import static `：导入指定类或接口中的静态结构（落脚点不是类，而是某个结构，属性或方法，或者写*），用的少。
```java
import static java.lang.System.*;
import static java.Math.*;
//这样可以省略System、Math
out.println("abc");
long a=round(1.23);
```
# 4. 继承
Java对继承的规定
1. 单继承：一个子类只能有一个父类
2. 直接继承的是直接父类，间接继承的是间接父类
3. 子类继承父类之后，就获取了直接父类以及所有间接父类中声明的属性和方法，但是能否访问还得看权限修饰符。

## 4.1 Object类
1. 如果我们没有显示地声明一个类的父类的话，则此类继承于`java.lang.Object`类
2. 所有的java类（除了java.lang.Object类）都直接或间接继承于java.lang.Object类
## 4.2 方法重写
- 定义：
在子类中可以根据需要对从父类继承来的方法进行改造。在程序执行时，子类的方法将覆盖父类的方法
- 要求：
1. 重写的方法必须与父类具有相同的**方法名和参数列表**
2. 重写的方法的返回值类型**不能大于**父类被重写的方法的返回值；如果父类返回的是基本数据类型，那么子类也必须是。
3. 子类重写的方法使用的访问权限**不能小于**父类被重写的方法的权限
4. **子类不能重写父类中声明为private权限的方法**
5. 子类方法跑出的异常不能大于父类被重写的方法的异常
- 注意：
子类与父类中同名同参数的方法必须同时声明为非static的（即为重写），或者同时声明为static（不是重写）。因为static方法属于类，子类无法覆盖父类的static方法。

## 4.3 super关键字
- 说明
1. 可以在子类的方法或构造其中，使用`super.属性`或者`super.方法`的方式，显示地调用父类中声明的方法或属性。**通常情况下， 省略super**
2. 特殊情况下，当子类和父类方法名或属性名重名时，显示调用super

- super调用构造器
3. 可以在子类的构造器中显示地使用`super(形参列表)`的方式，调用父类中声明的指定的构造器
4. 必须在子类构造器的首行
5. 类的构造器中，`this(形参列表)`和`super(形参列表)`只能二选一，不能同时出现
6. 在构造器的首行，如果没有显示地声明 `this(形参列表)`或`super(形参列表)`，则默认调用的是父类中空参的构造器；**注意，子类构造器必须直接或间接地调用父类构造器，如果没有声明，则调用父类的默认空参构造器（如果父类没有，则报错）**。
## 4.4 子类对象实例化过程
- 从结果上看
子类继承父类之后，就获取了父类中声明的属性和方法；创建子类的对象，在堆空间中，就会加载所有父类中声明的属性
- 从过程上看
当我们通过子类的构造器创建子类对象时，我们一定会直接或间接地调用其父类的构造器，进而调用父类的父类的构造器，直到调用了java.lang.Object类中的空参构造器为止。
正因为加载过所有父类的结构，所以才可以看到内存中有父类中的结构，子类对象才可以考虑进行调用。
<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210716110051281.png" width=70%>

# 5. 多态
## 5.1 概述
1. 理解多态性：是面向对象中最重要的概念，在Java中的体现就是**父类的引用指向子类的对象**。可以直接应用在抽象类和借口上。

2. Java**引用变量**有两个类型：**编译时类型**和**运行时类型**。编译时类型由声明该变量时使用的类型决定，运行时类型由实际给该变量赋值的对象决定。简而言之：**编译时看左，运行时看右**。
	- 若编译时和运行时类型不一致，就出现了对象的多态性（Polymorphism）
	- 多态情况下：
		- 看左边：看的是父类的引用（父类中不具备子类特有的方法）
		- 看右边：看的是子类的对象（实际运行的是子类重写父类的方法） 
3. 多态使用的前提
	- 类的继承关系
	- 方法的重写
4. 多态性不适用于属性（编译和运行时都看左边）
5. 多态的意义：多态是面向对象中最重要的概念，没有多态性，就没有后面的抽象类、接口等概念。
6. 虚拟方法调用：
子类中定义了与父类同名同参的方法，在多态情况下，将此时父类的方法成为虚拟方法，父类根据赋给他的不同子类对象，动态调用属于子类的该方法。**这样的方法调用在编译器是无法确定的**。
7. 如何证明多态是运行时行为？（编译时无法确定类型，动态绑定）
```java
package July.demo01;

import java.util.Random;

public class Animal {
    void speak(){

    }

    static Animal getInstance(int key){
        switch(key){
            case 0:
                return new Dog();
            case 1:
                return new Cat();
            default:
                return new Rabbit();
        }
    }
    public static void main(String[] args) {
        int key= new Random().nextInt(3);
        Animal animal=getInstance(key);
        animal.speak();
    }
}

class Dog extends Animal{
    void speak(){
        System.out.println("Dog!");
    }
}

class Cat extends Animal{
    void speak(){
        System.out.println("Cat!");
    }
}

class Rabbit extends Animal{
    void speak(){
        System.out.println("Rabbit!");
    }
}
```
## 5.2 重载和重写
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210716112757368.png)
## 5.3 instanceof 操作符与向下转型
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210717090623255.png)
如何才能调用子类特有的方法和属性？向下转型，使用强制类型转换符。使用强转的时候，可能出现`ClassCastException`异常。
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210717090412380.png)
`a instanceof A`：如果a是A的实例，或者是A的子类的实例，则返回true

***
对于引用数据类型来说，强制类型转换的前提是，堆内存中存在转换后类型（或子类）的对象，否则不能转换。
```java
//Man是Person的子类
Person p=new Person();
Man m=(Man)p;	//错误！堆内存中是Person类型对象

Object obj=new Man();
Person p=(Person)obj;	//可以执行！内存中有Man类型对象，也是Person子类的实例。
```
# 6. 补充内容
## 6.1 Object类
1. Object类是所有Java的根父类
2. 如果定义一个类没有声明父类，则默认继承java.lang.Object
3. Object类只声明了一个空参的构造器
4. Object类方法（所有类的通用方法）
	- equals
	- toString
	- getClass
	- hashCode
	- clone
	- finalize
## 6.2 ==和equals
- `==`运算符
	1.比较基本数据类型：比较值。**注意可以类型不同**，如int a=1; float b=1.0，a和b`==`运算的结果是true。（boolean不能参与运算）
	2.比较引用数据类型：比较在内存中的地址值。
	- 为何有些情况下，直接定义String的时候，可以直接用`==`判断两个String值相等？因为String存在字符串常量池的问题（但不是所有String都在里边，也有可能两个相同的String不指向同一个引用）。
- equals方法
1.只能用于引用数据类型
2.Object中的equals是用`==`实现的
3.IDE自动生成的equals方法
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/2021071709354233.png)

## 6.2 包装类
- 基本数据类型、包装类和String之间的转换
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210717100724191.png)
- 自动装箱与自动拆箱
包装类和基本数据类型之间可以直接进行互相赋值
```java
int a=1;
Integer b=a;
a=b;
```
- 基本数据类型、包装类-->String
方式一：使用`""+`连接
方式二：`String.valueOf()`
- String-->基本数据类型、包装类
<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210717102102738.png" width=70%>

- 包装类的一些问题
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210717104018194.png)
## 6.3 关键字：static
1. 修饰属性
2. 修饰方法
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210718085218111.png)
- 单例设计模式
	- 饿汉式： 
		- 优点：线程安全
		- 缺点：资源效率不高，可能getInstance()永远不会执行到，但执行该类的其他静态方法或者加载了该类，那么这个实例仍然初始化
	- 懒汉式：
		- 优点：延迟加载，需要用到实例的时候再加载
		- 缺点：线程不安全（**有改进的方法**） 	
```java
//单例之饿汉式
public class House {
    private static House house=new House();

    public static House getInstance(){return house;}

    private House(){}

}

//单例之懒汉式
class Country{
    private Country(){}

    private static Country count=null;

    public static Country getInstance(){
        if(count==null)
            count=new Country();
        return count;
    }
    
}
```
- 单例模式应用场景
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210718094329826.png)
## 6.4 类的成员：代码块
1. 代码块的作用：用来初始化类、对象
2. 代码块如果有修饰的话，只能使用static
3. 可以定义多个，按照声明的先后顺序执行
4. 分类：
	- 静态代码块：随着类的加载而加载，并执行；随着类的加载而执行，而且只执行一次
	- 非静态代码块：随着对象的创建而加载，并执行；每创建一个对象，就执行一次
## 6.5 关键字：final
1. 修饰类：不能被继承
2. 修饰方法：不能被重写
3. 修饰变量：此时的变量变为了一个**常量**。 
	- 基本数据类型：变量一旦赋值后，不能被重新赋值。
	- 引用数据类型：引用指向不能发生改变。
	![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/2021071810193489.png)
## 6.6 抽象类与抽象方法
 abstract可以用来修饰**类、方法**，不能修饰私有方法、静态方法、final的类、方法
 1. 修饰类：抽象类

 	- 此类不能实例化
 	- 仍然提供构造器（子类要调用），类中一定有构造器。
 2. 修饰方法：抽象方法

 	- 抽象方法只有方法的声明，**没有方法体**
 	- 包含抽象方法的类，一定是一个抽象类。反之不然。
 	- 子类重写父类中的所有抽象方法后，此子类方可实例化

抽象类的匿名子对象
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210718104855919.png)
模板方法设计模式

**抽象类和接口的区别**
```
抽象类是一种对事物的抽象，而接口是一种对行为的抽象； 抽象类是对整个类整体进行抽象，包括属性、行为，但是接口却是对类局部（行为）进行抽象。 抽象类是一种模板式设计，而接口是一种行为规范，是一种辐射式设计。
```
## 6.7 接口
- Java不支持多继承，但是有了接口，就可以实现多重继承的效果。**接口就是规范**，定义的是一组规则，体现了现实世界中“如果你是/要...则必须能...”的思想。
 - 说明：
 1. interface接口不能定义构造器，意味着接口不能实例化。
 2. 类implements接口
 3. 如果类**没有**实现接口中的所有抽象方法，则此类仍然是一个抽象类
 4. **接口和接口之间可以继承，而且可以多继承**
 5. JDK8之后，除了定义全局常量**public static final**和抽象方法以外，还可以定义**静态方法、默认方法**。
 6. 接口中的所有属性（全局静态常量）和方法，都是public的，即使不加，系统也自动声明为public。

- 接口匿名实现类
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210718165953843.png)
## 6.8 接口应用：代理模式（Proxy）
- 概述：
代理模式是Java开发中使用比较多的一种设计模式。代理设计就是为其他对象提供一种代理以控制这个对象的访问。如明星（被代理类）和经纪人（代理类），租房者（被代理类）和中介（代理类）。
```java
public class proxyPatternDemo {
    public static void main(String[] args) {
        Proxy proxy=new Proxy(new Server());
        while(true){
            proxy.browse();
            System.out.println();
        }
    }
}

//浏览网页的接口
interface NetWork {
    void browse();
}

//被代理类
class Server implements NetWork{
    @Override
    public void browse() {
        System.out.println("正在访问该网站");
    }
}


//代理类
class Proxy implements NetWork{

    private NetWork netWork;

    public Proxy(NetWork netWork){
        this.netWork=netWork;
    }

    private boolean check() throws InterruptedException {

        System.out.println("正在做访问网络前的检查...");
        Thread.sleep(1000);
        return new Random().nextInt(100)%2==0;
    }
    @Override
    public void browse(){
        try {
            boolean res=check();
            if(res){
                System.out.println("校验通过，允许访问！");
                netWork.browse();
            }else{
                System.out.println("校验不通过，不允许访问！");
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

```
- 应用场景
  ![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/2021071817421576.png)
- 分类
	- 静态代理（静态定义代理类）
	- 动态代理 （动态生成代理类）
		- JDK自带的动态代理，需要反射等知识。 
## 6.9 接口的应用：工厂设计模式
- 工厂模式概念：实现了**创建者与调用者的分离**，即将创建对象的具体过程屏蔽隔离起来，达到提高灵活性的目的。
	- 核心本质：实例化对象，用工厂方法代替new操作；将选择实现类、创建对象统一管理和控制。从而将调用者跟我们的实现类解耦。 
- 工厂模式的分类
	- 简单工厂模式：用来生产同一等级结构中的任意产品。（对于新增加的产品，需要修改已有代码） 
	- 工厂方法模式：用来生产同一等级结构中的固定产品。（支持增加任意产品）
	- 抽象工厂模式：用来生产不同产品族的全部产品。（对于增加新的产品，无能为力；支持增加产品族） 

//简单工厂模式
```java
public interface Car {
    public void run();
}

class BMW implements Car{
    @Override
    public void run(){
        System.out.println("BMW is running~");
    }
}

class Mercedes_Benz implements Car{
    @Override
    public void run(){
        System.out.println("Benz is running~");
    }
}

//Car工厂类
class carFactory{
    public static BMW getBMW(){
        return new BMW();
    }
    public static Mercedes_Benz getBenz(){
        return new Mercedes_Benz();
    }
}

class Test{
    public static void main(String[] args) {
        Car car1=carFactory.getBenz();
        Car car2=carFactory.getBMW();
        car1.run();
        car2.run();
    }
}
```
//工厂方法模式
```java
public interface Car {
    void run();
}

class Benz implements Car{
    @Override
    public void run() {
        System.out.println("Benz is running...");
    }
}

class Audi implements Car{
    @Override
    public void run() {
        System.out.println("Audi is running...");
    }
}

//工厂接口
interface carFactory{
    Car getCar();
}

//宝马工厂
class BenzFactory implements carFactory{
    @Override
    public Benz getCar() {
        return new Benz();
    }
}
//奥迪工厂
class AudiFactory implements carFactory{
    @Override
    public Car getCar() {
        return new Audi();
    }
}

class Test{
    public static void main(String[] args) {
        Car car1=new AudiFactory().getCar();
        Car car2=new BenzFactory().getCar();
        car1.run();
        car2.run();
    }
}

```
## 6.10 JDK8接口静态方法、默认方法
- 静态方法：接口中定义的静态方法，只能通过接口来调用（不能用实现类来调用）
- 默认方法：类似让接口拥有类的功能，让实现类拥有该方法。通过实现类对象来调用（可以重写）。
- public即使不加，系统也会自动加上
```java
public interface Test {
    public static void method1(){
        //...
    }
    
    static void method2(){
        //...
    }
    public default void method3(){
        //...
    }

    default void method4(){
        //...
    }
}

```
## 6.11 内部类
- 概念：当一个事物的内部，还有一个部分需要一个完整的结构进行描述，而这个内部的完整的结构又只为外部事物提供服务，那么整个内部的完整结构最好使用内部类。
- Inner class 一般用在定义它的类或者语句块之内，在外部引用它时必须给出完整的名称。
- 分类
	- 成员内部类：static成员内部类和非static成员内部类
	- 局部内部类（不谈修饰符）：匿名内部类 
```java
public class Person {
    //静态成员内部类
    static class Cat{}
    //非静态成员内部类
    class Dog{}

    void f(){
        //局部内部类
        class Fish{}
    }   
}
```
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210719105503188.png)
# 7. 异常处理
## 7.1 异常概述与异常体系结构
- 为什么需要异常处理
因为有很多问题不是靠代码能够避免的，比如：**用户输入数据的格式，读取文件是否存在，网络是否始终保持畅通**等等
- 异常：在程序执行过程中发生的不正常情况称为“异常”。（语法错误和逻辑错误不是异常）
- 分类
	- Error：Java虚拟机无法解决的严重问题。如：JVM系统内部错误、资源耗尽等严重情况。如：StackOverflowError和OOM。一般不编写针对性的代码进行处理
	- Excepetion：其他因编程错误或者偶然的外在因素导致的一般性问题，可以使用针对性的代码进行处理，例如：
		- 空指针访问
		- 试图读取不存在的文件
		- 网络连接中断
		- 数组访问越界  
- Exception又分为：编译时异常(checked受检异常）和运行时异常
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210719132747304.png)


## 7.2 常见异常
**编译时异常**

- IOException
	----FileNotFoundException
- ClassNotFoundException

**运行时异常**
（编程实践中，一般对运行时异常不做处理）

- NuLLPointerException
```java
//试图堆一个空对象调用方法时，就会产生空指针异常
public class Main {
    @Test
    public void test(){
        Date date=null;
        date.getTime();
    }
}
```
- ArrayIndexOutOfBoundsException
- ClassCastException
```java
public class Main {
    @Test
    public void test(){
        Object obj=new String("abc");
        Main main=(Main)obj;
    }
}
```
- NumberFormatException
```java
public class Main {
    @Test
    public void test(){
        int num=Integer.parseInt("abc");
    }
}
```
- InputMismatchException
Scanner输入int，结果用户输入abc，会报此异常
- ArithmeticException
```java
public class Main {
    @Test
    public void test(){
        int a=10/0;
    }
}
```

## 7.3 异常处理机制一：try-catch-finally
使用`try-catch-finally`处理编译时异常，使得程序在编译时就不再报错，但是运行时仍可能报错。相当于把一个编译时可能出现的异常，**延时到运行时出现（转变为运行时异常）**。所以，对于运行时异常，一般不做处理。
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/dcc4da8a7df24234b02a3959ba1a379e.png)
使用try将可能出现异常的代码包装起来，在执行过程中，一旦出现异常，就会生成一个对应异常的类，**并且终止try中代码的执行**，根据此异常的类型去catch中进行匹配。<font color='red'>try-catch-finally执行完了，继续执行下面的其他代码。</font>

常用的异常对象处理的方式
1.`String getMessage()`
2.`void printStackTrace()`

try-catch-finally结构可以嵌套

注意：如果catch多个异常存在父子关系的话，子类要放在上边
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/ee1698672fd14d088e5e8b9e3eb1db08.png)

## 7.4 异常处理机制二：throws
1.使用throws向上抛出异常，出现异常时，**异常后序的代码将不再执行**
2.throws的方式只是将异常抛给了方法的调用者，并没有真正将异常处理掉。

**注意**：子类重写方法抛出的异常类型不大于父类被重写的方法抛出的异常类型（或者不抛）
（原因：多态时，父类引用指向子类对象，调用同名方法，并且进行异常处理时，如果子类方法异常类型大于父类类型，则无法处理）

```java
public class Main {
    @Test
    public void test() {
        f(new Son());
    }
    
    public void f(Father person){
        try {
            person.method();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

class Father{
    void method()throws IOException{
        
    }
}

class Son extends Father{
    void method()throws FileNotFoundException{
        
    }
}
```
## 7.5 两种异常处理方式的选择
1.如果父类中被重写的方法没有throws方式处理异常，则子类重写的方法也不能使用throws，意味着如果子类重写的方法中有异常，必须使用`try-catch-finally`方式处理
2.执行的方法A中，先后又调用了另外几个方法bcd，这几个方法是递进关系的（需要利用上一个方法的返回值）。建议这几个方法使用throws进行处理。而执行的方法A考虑使用`try-catch-finally`方式进行处理。（如果在b直接进行try-catch处理，cd会正常执行，但是利用的返回值是无效的）

## 7.6 手动抛出异常：throw
关于异常对象的产生：
1.系统自动生成的异常对象
2.手动的生成一个异常对象，并**throw**
```java
public class ThrowTest {
    public static void main(String[] args) {
        new Student(-100);
    }
}

class Student{
    private int id;
    Student(int id){
        if(id>0)
            this.id=id;
        else
            throw new RuntimeException("输入的id非法");
    }
}
```
运行结果
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/43e1b48fb31e41fbaa52203a82f82a5b.png)
```java
public class ThrowTest {
    public static void main(String[] args) {
        try {
            new Student(-100);
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}

class Student{
    private int id;
    Student(int id) throws Exception{
        if(id>0)
            this.id=id;
        else
            throw new Exception("输入的id非法");
    }
}
```
运行结果
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/a1d0486764844e8d9634f39543782e54.png)

## 7.7 用户自定义异常类
如何自定义异常类?
1.继承于现有的异常结构：RuntimeException, Exception
2.提供全局常量序列号: serialVersionUID  (类的唯一标识）
3.提供重载的构造器

```java
class MyException extends RuntimeException {
    static final long serialVersionUID = 123L;

    public MyException() {
    }
    
    public MyException(String msg) {
        super(msg);
    }
}
```