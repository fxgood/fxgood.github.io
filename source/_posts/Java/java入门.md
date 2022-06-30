---
date: 2020-01-29
title: Java入门
category: Java
tag: 编程语言
excerpt: 将大一学习的java重新拿出来学一遍，同时做一些笔记。
---

# 第一章 IDEA基本配置和快捷键
## IDEA快捷键
| 快捷键               | 功能                                   |
| -------------------- | -------------------------------------- |
| `shift+F6`           | 选中目标内容后，更改所有用到它的内容   |
| `ctrl+Y`             | 删除当前行                             |
| `ctrl+D`             | 复制当前行                             |
| `Alt+Enter`          | 导入包自动修正代码                     |
| `Ctrl+Alt+L`         | 格式化代码                             |
| `Ctrl+/`             | 单行注释，再按取消                     |
| `ctrl+shift+/`       | 选中代码注释，再按取消                 |
| `Alt+Ins`            | 自动生成代码：如toString,set,get等方法 |
| `Alt+Shift+上下箭头` | 移动当前行                             |
| `Shift+F6`           | 更改类文件名称                         |
| `ctrl+alt+t`         | 包括代码块，添加try-catch等信息        |
| ## 操作技巧          |                                        |
 * 5.fori 可以快速输入循环体 for(int i=0;i<5;i++)
 * arryA.fori可以快速输入循环体for(int i=0;i<arr.length;i++)
 * Alt+Enter 快速为对象.方法 创建一个接收者
 * IDEA注释最好看的颜色#088D52

***
# 第二章 JAVA基本知识
## 基础知识
1. 所有的引用类型都可以赋值为null，代表其中什么都没有
2. 获取数组长度  `数组名称.length`
## 方法
### 使用方法的注意事项
 	1.方法应该定义在类中，不能在方法中定义方法
 	2.方法定义的前后顺序无所谓
 ### 方法重载
 	1.要求具有不同的参数列表（参数个数不同，参数数据类型不同，参数的数据类型排列顺序不同，满足其一即可）
 	2. 修饰符如`public,static`等无关
## 数组
### 数组的初始化
* 动态初始化（指定长度）

```java
int[]arr=new int[10];
//或者拆分成两个步骤
int[]arr;
arr=new int[10];
```

* 静态初始化（指定内容）

```java
int[]arr=new int[]{1,2,3};
//或者分为两步
int[]arr;
arr=new int[]{1,2,3};
```
* 数组省略格式(**无法拆分成两个步骤**）

```java
int[]arr={1,2,3};
```
### 数组的访问
* 使用动态初始化时，其中的元素会自动拥有一个默认值，规则如下:
	1. 如果是整数类型，默认为0
	2. 如果是浮点类型，默认为0.0
	3. 如果是字符类型，默认为'\u0000' （是一个不可见字符）
	4. 如果是布尔类型，默认为false
	5. 如果是引用类型，那么默认为null(空常量）
### 利用数组实现多个返回值


## JAVA内存
* java的内存需要划分为5个部分：
    1. 栈(Stack):存放的都是方法中的局部变量，**方法的运行一定要在栈中**
						局部变量：方法的参数，或者是方法内部的变量
						作用域：一旦超出作用域，立刻从栈内存当中消失
    2. 堆(Heap):**凡是new出来的东西，都在堆当中**
    					堆内存里面的东西都有一个地址值：16进制
    					堆内存里面的数据，都有默认值，规则在上 	面。
    3. 方法区(Method Area):存储.class(**.java编译之后用来运行的字节码文件**)是相关信息，包含方法的信息
    4. 本地方法栈(Native Method Stack):与操作系统相关
    5. 寄存器(pc Register):与CPU相关
### 数组在内存中的存储
* 内存中的两个不同数组
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/2020012115282069.png)    												   
* 两个引用指向同一个数组
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20200121153141754.png)
### 对象在内存中的存储
* 当一个对象作为参数传递到方法中时，实际上传递进去的是**地址值**
* 当一个对象类型作为方法的返回值时，返回值其实就是对象的**地址值**
* 一个对象在内存中的存储
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/2020012118051896.png)
* 两个引用指向同一个对象
![](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20200121182108878.png)
## final关键字概念和四种用法
* 常见四种用法：1.修饰一个类
2.修饰一个方法
3.修饰一个局部变量
4.修饰一个成员变量
* 注意事项：对于类和方法来说，`abstract`和`final`不能同时使用，因为矛盾
* final修饰一个类时`public final class...`
含义：当前类不能有任何子类
* final修饰一个方法时`修饰符 final 返回值类型`‘
含义：该方法不能被覆盖重写 
* final修饰变量，一次赋值，终身不变
含义：1.对于基本数据类型来说，不可改变的是数值
2.对于引用数据类型来说，不可改变的是地址值
* final修饰成员变量，这个变量也是不可变的
1.由于成员变量具有默认值，所以用了final之后必须手动赋值，不会再给默认值
2.对于final成员变量，要么直接赋值，要么通过构造方法赋值（必须保证所有重载的构造都对final成员变量赋值）
## 四种权限修饰符
public > protected > (default) > private 
* 同一个类中
* 同一个包中
* 不同包子类（父子关系）
* 不同包非子类（无关系）

|                                                              | public | protected | (default) | private |
| ------------------------------------------------------------ | ------ | --------- | --------- | ------- |
| 同一个类                                                     | √      | √         | √         | √       |
| 同一个包                                                     | √      | √         | √         | ×       |
| 不同包子类                                                   | √      | √         | ×         | ×       |
| 不同包非子类                                                 | √      | ×         | ×         | ×       |
| ## 内部类                                                    |        |           |           |         |
| 分类：1.成员内部类 2.局部内部类（包含匿名内部类）            |        |           |           |         |
| ### 成员内部类                                               |        |           |           |         |
| 格式：直接定义在类体内就行                                   |        |           |           |         |
| 注意：内用外，随意访问；外用内，需要内部类对象               |        |           |           |         |
| 如何使用内部类？有两种方式：                                 |        |           |           |         |
| 1.间接方式：在外部类的方法中使用内部类，然后main方法中调用外部类的方法 |        |           |           |         |
| 2.直接方法，公式：`外部类名称.内部类名称 对象名=new 外部类名称().new 内部类名称();` |        |           |           |         |
* 内部类的同名变量访问
```java
public class Outer {
    int num=10;
    public class Inner{
        int num=20;
        public void method(){
            int num=30;
            System.out.println(num);
            System.out.println(this.num);
            System.out.println(Outer.this.num);
        }
    }
```
### 局部内部类
如果一个类是定义在方法内部的，那么这就是一个局部内部类
**局部的含义**：只有当前的方法能使用它，出了这个方法就不能使用
格式:
```java
修饰符 class 外部类名称{
	修饰符 返回值 外部类方法名称(参数列表){
		class 局部内部类名称{
			...
		}
}
```
* 局部内部类使用方法
在成员方法中new局部内部类对象，然后调用成员方法即可

### 小结：类的权限修饰符
1.外部类：public / (default)
2.成员内部类:public / protected / (default) / private
3.局部内部类：什么都不能写
### 局部内部类的final问题
局部内部类中，如果希望访问所在方法的局部变量，那么这个局部变量必须是*有效final*的.
备注：从JAVA8+开始，只要局部变量**事实不变**，那么final关键字可以省略
原因：
1.new出来的对象是在**堆**当中的
2.局部变量是跟着方法走的，在**栈**内存中
3.方法运行结束后，立刻出栈，局部变量就会立刻消失
4.但是new出来的对象会一直存在于栈内存中，直到垃圾回收消失。因此该对象会copy一份使用到的变量，方法消失后保证数据依旧存在。故该方法的局部变量不能改变。
### 匿名内部类（重点）
如果接口的实现类（或父类的子类）只需要使用唯一的一次，那么就可以省略掉该类的定义，而改为使用**匿名内部类**
匿名内部类的使用格式：
*其中，obj是匿名内部类的对象，匿名内部类省略了繁琐地完整创建一个.class文件*
```java
public class Temp {
    public static void main(String[] args) {
        MyInterface obj=new MyInterface() {
            @Override
            public void method() {
                System.out.println("使用了匿名内部类！");
            }
        };
        obj.method();
    }
}
```
* 使用匿名内部类的注意事项
对格式*new 接口名称(){...}*进行解析
1.new代表创建对象的动作
2.接口名称就是匿名内部类需要实现的接口
3.*{...}*这才是匿名内部类的内容
* 还需注意
1.匿名内部类，在**创建对象时**，只能使用一次（但**不是匿名对象**，创建出来的对象可以一直使用）
2.**匿名内部类和匿名对象不是一回事**
3.匿名内部类和匿名对象同时使用
```java
public static void main(String[] args) {
        new MyInterface(){

            @Override
            public void method() {
                System.out.println("使用了匿名内部类和匿名对象");
            }
        }.method();
    }
```
***
# 第三章 面向对象程序设计
* 面向对象技术三大特性：`继承性、封装性、多态性`
## 基础知识
* 成员变量是直接定义在类中的，在方法体外边
* **成员方法不要写static关键词**
* 通常情况下，需要根据类创建一个对象才能使用
* 使用一个类的步骤
  1. 导包
  	import 包名称.类名称;
  	import cn.hhu.day06.demo01.Student;
  	**若位于同一包下无需导入**
  2. 创建
  	Student stu=new Student();
  3. 使用
  	如果成员变量没有赋值，那么将会有一个默认值，规则和数组一样
* 成员变量和局部变量的区别
	1. 定义的位置不同
		* 局部变量：在方法的内部
		* 成员变量：在方法的外部，直接写在类当中
	 2. 作用域不同
	
	 	* 局部变量：只有方法当中才可以使用
	 	* 成员变量：整个类全都可以用
	 3. 默认值不同
	
	 	* 局部变量：没有默认值，必须手动赋值
	 	* 成员变量：**有默认值**，规则和数组相同
	 4. 内存的位置不同（了解）
	
	 	* 局部变量：位于栈内存
	 	* 成员变量：位于堆内存（new出来的都放在堆里，赋初值）
	 5. 生命周期不同
	
	 	* 局部变量：随着方法进栈诞生，随着方法出栈消失
	 	* 成员变量：随着对象创建诞生，随着对象被垃圾回收而消失
* 匿名对象的说明
  匿名对象就是只有右边的对象，没有左边的名字和赋值运算符
  

```java
new Person().name="赵丽颖";	//只能使用这一次
 
```
## 抽象
* 抽象方法：加上abstract关键字，然后去掉大括号，直接分号结束
* 抽象类：抽象方法所在的类必须是抽象类。在class之间写上abstract即可
* 如何使用抽象类和抽象方法
  1. 不能直接new抽象类对象，必须用一个子类继承抽象父类
  2. 子类必须覆盖重写（也叫实现）父类中的所有抽象方法
* 注意事项
  1. 抽象类中，可以有构造方法，供子类创建对象时，初始化父类成员时使用
  2. 抽象类中不一定包含抽象方法，但是包含抽象方法的必须是抽象类
  3. 除非子类也是抽象类，否则必须重写抽象父类中的所有抽象方法
  4. 抽象类**不能创建对象**，无论是否含有抽象方法
## 继承性
* JAVA语言是**单继承的**（一个类只能有一个直接父类），但是可以**多级继承**
* 如果类的定义中不含选项`extends父类名`，则java虚拟机一般会自动给当定义的类添加默认的选项`extends java.lang.Object`。故除了类java.lang.Object之外，任何类都有父类，而且**类java.lang.Object是除它自身之外的所有类的父类**。
* 当前定义的类的构造方法必须调用其父类的构造方法，而且该调用语句必须是当前定义的类的构造方法的第一句语句，其调用格式是
* 如果发生重名，成员方法属于谁，就优先用谁的成员变量
```java
super(调用参数列表)
//其中super是关键字，表示直接父类的构造方法。
```
* `java.lang.Object`类是所有类的	公共最高父类
### 三种重名
* **成员变量重名**
父类成员变量使用super.
子类成员变量使用this.
局部变量直接写

```java
//Father.java
public class Father {
    int num=30;
}
//Son.java
public class Son extends Father {
    int num = 20;

    public void method() {
        int num = 10;
        System.out.println("Father:" + super.num);
        System.out.println("Son:" + this.num);
        System.out.println("Temp:" + num);

    }
}

```
* **成员方法重名**

### 方法覆盖
1. 必须保证父子类之间方法的名称相同，参数列表也相同
2. 子类方法的返回值必须小于等于父类方法的返回值范围
3. `@Override`:这叫做**注解**，写在方法前面，用来检测是否有效地正确覆盖。（如果报错说明未发生覆盖）
4. 子类方法的权限必须大于等于父类方法的权限修饰符。
    public>protected>留空(default)>private
### 继承中构造方法的访问特点
1. 子类构造方法当中，有一个默认隐含的`super()`调用，所以**一定是先调用父类构造方法，后执行子类构造方法**
2. 可以通过`super`关键字在子类中调用父类**重载构造函数**
3. super的父类构造调用，必须是子类构造方法的第一个语句。不能多次调用super构造。
### super关键字的三种用法
### this关键字的三种用法
1. 在本类的成员方法中，访问本类的成员变量（成员方法中含有重名的局部变量，也生效）
2. 在本类的成员方法中，访问本类的另一个成员方法
3. 在本类的构造方法中，访问本类的另一个重载构造方法

```java
    public Temp01(){
        System.out.println("调用无参构造函数");
    }
    public Temp01(int n){
        this();
        System.out.println("调用重载构造函数");
    }
```

第三种方法中注意：**this()构造方法也必须是语句的第一句**
注意：this和super两种构造调用不能同时使用，**因为构造方法必须放在第一句，也就必须要保证唯一性**
### 子类与父类之间的类型转换
1. 隐式类型转换：无需强制类型转换符，将子类型的数据转换成父类型数据

```java
Teacher tom=new Teacher();
Employee a=tom;
```
2. 显式类型转换：需要强制类型转换符`()`，将父类型数据转换为子类型数据

```java
Teacher tom=new Teacher();
Employee a=tom;
Teacher b=(Teacher)a;
```
3. 如果两个类型不存在子类型与父类型之间的关系，则一般不能进行类型转换。可以使用`引用类型表达式instanceof引用类型`来判断一个引用表达式所指向的实例对象是否是某种引用类型的实例对象

```java
Teacher a=new Teacher();
Employee b=new Employee();
Employee c=a;
```
则表达式`a instanceof Teacher`返回true
表达式`a instanceof Employee`返回true
表达式`b instanceof Teacher`返回false
表达式`c instanceof Employee`返回true
表达式`c instanceof Teacher`返回true
* `instanceof`运算符可以用再引用类型转换中，**即先判断一个引用表达式所指向的实例对象是否是目标类型的实例对象，可以避免错误**如

```java
if(a instanceof Teacher)
	b=(Teacher)a;
else b=new Teacher();
```
## 多态性
 * 多态性指的是：在类定义中出现多个构造方法或者出现多个同名的成员方法。对于同名的成员方法，多态性还包括在当前定义的类型中出现与其父类型的成员方法同名的成员方法。**多态性包括：静态多态性和动态多态性**。
### 静态多态性
 * `静态多态性`指的是在同一个类中同名方法在功能上的重载`overload`，这也包括一个类对其父类同名方法在功能上的重载，而且在方法声明的形式上要求同名的方法具有不同的参数列表。这里的**方法**，可以是成员方法，也可以是构造方法。一般建议，重载的方法应当具有相似的功能。
### 动态多态性
 * 动态多态性指的是在子类和父类的类体中均定义了具有基本相同声明的非静态成员方法。所谓**非静态成员方法**指的是在成员方法定义中成员方法的声明不包含方法修饰词static，这时也称为子类的成员方法对其父类进本相同声明的成员方法的覆盖**override**。（**基本相同声明的成员方法**要求具有相同的方法名，相同的参数个数，对应参数的类型也相同，而且子类的成员方法应当比其父类对应的成员方法有更广的访问控制方式）
 * **利用动态多态性使得可以通过父类型的引用调用子类型的成员方法**。这里要求父类型的引用 所指向的实例对象实际上是其子类型的实例对象，而且调用的子类型的成员方法是对父类型同名成员方法的覆盖。
 * 由于动态多态性使得子类型的成员方法屏蔽了父类型的被覆盖的成员方法，因此通过关键字`super`可以解决在子类型的成员方法中调用父类型的被覆盖的成员方法的问题。

 	1. 在子类型的非静态成员方法中访问其父类型的成员与，格式：
 		`super.父类型的成员域`
 	2. 在子类型的非静态成员方法中调用其父类型的成员方法，其格式：
 		`super.父类型的成员方法(调用参数列表)`
 	3. 在子类的构造方法的第一条语句处调用其父类的构造方法，其格式：
 		`super(父类构造方法的调用参数列表)`;
* 与`super`对应的关键字是`this`，其调用的是同一个类的成员域或成员方法。
* 需要注意的是，**动态多态性只针对非静态的成员方法**。
## 封装性
* 封装性在JAVA中的体现
	1. 方法是一种封装
	2. 关键字private也是一种封装
		一旦使用private进行修饰，在本类中可以随意访问，但是超出本类范围就不能再直接访问了。
* 间接访问private成员变量，就是定义一对儿get/set方法，**必须叫getXXX/setXXX**。**对于基本数据类型中的boolean类型，get方法必须写成isXXX**。 
* 当方法的局部变量（如参数）和类的成员变量重名的时候，根据*就近原则*，优先使用局部变量。如果需要访问本类当中的成员变量，需要使用`this.成员变量名`
* **通过谁调用的方法，谁就是this **
## 构造方法
* 构造方法注意事项
	1. 构造方法的名称必须和所在的类名完全一样
	2. 构造方法无返回值
	3. 如果没有编写任何构造方法，那么编译器会默认一个构造方法，没有参数，方法体什么事都不做
	4. 构造方法可以进行重载
## 一个标准的类
* 一个标准的类要拥有下面四个部分：
 1. 所有的成员变量都要使用private关键字修饰
 2. 为每一个成员变量编写一对get/set方法
 3. 编写一个无参数的构造方法
 4. 编写一个全参数的构造方法

这样标准的类也叫做	Java Bean. 可以使用快捷键`Alt+Insert`或菜单中`Code-Generate`
## Static关键字
* Static修饰成员field
只在类中保存一份，所有本类对象共享一份。属于类而不是属于单独某一个对象。
可以利用这一点，自动给对象顺序赋id

```java
    private String name;
    private int id;
    static String teacher="胡景月";
    private static int idCount=0;   //给成员变量赋初值

    public Student(String name) {
        this.name = name;
        id=++idCount;
    }
```
* Static修饰方法
  1. 静态方法属于类，不属于对象
  2. 如果没有static，必须创建对象才能使用方法
  3. 如果有static，对于静态方法来说，可以通过对象调用方法（不推荐），也可以通过类名调用方法。
  4. 本类当中的静态方法可以省略类名
  5. **静态不能直接访问非静态**（原因：内存中先有的静态内容，后有的非静态内容）
  6. 静态方法中不能用this，因为this指的是当前对象，而静态内容属于类而不属于对象
* 静态Static的内存图
  静态内容与对象无关，即使写成`对象.静态内容`,也会被编译成为`类.静态内容`
  ![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20200123204642537.png)
* 静态代码块
特点：1. 第一次用到本类时，静态代码块执行唯一的一次
2. **静态内容总是优于非静态，所以静态代码块比构造方法先执行**
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20200123205603472.png)
* 静态代码块的典型用途
1. 一次性地对静态成员进行赋值
2. 
# 第四章 API
## API概念
API全程Application Programming Interface,应用程序编程接口。
## API使用步骤
1. 打开帮助文档
2. 点击显示，找到索引，看到输入框
3. 输入框里输入想找的类
4. 看包。java.lang下的类不需要导包，其他需要
5. 看类的解释和说明
6. 学习构造方法
7. 使用成员方法
## 
## Scanner类——键盘输入类
* 基本功能：可以实现键盘输入数据到程序当中
* 引用类型的一般使用步骤：
 1. 导包

 	如果需要使用的目标类和当前类位于同一包下，则可以省略导包语句；只有java.lang包下的内容(如java.lang.String)不需要导包，其它的包都需要import语句。
 2. 创建
      Scanner sc=new Scanner(System.in);	//代表从键盘输入
 3. 使用
      获取键盘输入的一个int数字：int num=sc.nextInt();
      其实键盘输入的都是字符串， **nextInt是将输入的字符串里的数字转换成int型数**
      获取键盘输入的一个字符串：String str=sc.next(); 
    **如果只需使用一次，可以使用匿名对象**


```java
int num=new Scanner(System.in).nextInt();
//还可以使用匿名对象作为参数
public class Demo04Anonymous {
    public static void printInt(Scanner sc){
        int temp=sc.nextInt();
        System.out.println("Num is "+temp);
    }

    public static void main(String[] args) {
        printInt(new Scanner(System.in));
    }
}
//匿名对象还可以作为返回值
public static Scanner methodReturn() {
        return new Scanner(System.in);
    }
```
## Random类——产生随机数字
* random类的使用
  

```java
Random r=new Random();
        int a=r.nextInt();  //范围-21亿到+21亿
```
* Random生成指定范围的随机数
```java
Random r=new Random();
        for (int i = 0; i < 10; i++) {
            int temp = r.nextInt(10);
            //范围是[0,10) 左闭右开
            System.out.print(temp);
            System.out.print(" ");
        }
//若想1~10就 
r.nextInt(10)+1;
       
```
## 对象数组

```java
Person[]arr=new Person[3];
        Person one=new Person("余丰旭",22);
        Person two=new Person("陈香玉",24);
        Person three=new Person("小小余",1);
        arr[0]=one;
        arr[1]=two;
        arr[2]=three;
```
## ArrayList
* 数组的长度不能改变，但是ArrayList集合的长度是可以随意变化的
* 对于ArrayList来说，有一个尖括号<E>代表泛型，也就是装在集合当中的所有元素，全都是的什么类型。
* 注意：**泛型只能是引用类型，不能是基本类型。**原因是：ArrayList中存储的都是引用类型对象的地址，而基本数据类型没有地址。
  解决方案：**使用基本类型对应的包装类**。
  包装类：都位于java.lang包下（无需导包）
  从JDK1.5+开始，支持自动装箱、自动拆箱。
  自动装箱：基本类型-->包装类型
  自动拆箱：包装类型-->基本类型
  
| 基本类型 | 包装类    |
| -------- | --------- |
| byte     | Byte      |
| short    | Short     |
| int      | Integer   |
| long     | Long      |
| float    | Float     |
| double   | Double    |
| char     | Character |
| boolean  | Boolean   |
* 对于ArrayList集合来说，直接打印的到的不是地址值，而是内容

```java
ArrayList<String> list = new ArrayList<>();
        list.add("余丰旭");
        list.add("陈香玉");
        list.add("小小余");
        System.out.println(list);
```
* ArrayList常用方法
  1. public boolean add(E e):向集合当中添加元素，类型与泛型一致
  2. public E get(int index):从集合中获取元素，参数是索引编号
  3. public E remove(int index):从集合中删除元素
  4. public int size():获取集合的尺寸长度（集合中的元素个数）
* 注意，**ArrayList作为方法参数时，不要忘了\<E(泛型)\>**
## String
### 一.String类基本知识
* java中只要是" "中的内容，都是String实例，无论new否
*   字符串特点
 1. **字符串的内容永不可变**
 2. 正是因为字符串不可改变，所以字符串是可以共享使用的
 3. 字符串效果上相当于是char[ ]字符数组，但是底层原理是byte[ ]字节数组
* 字符串的构造方法和直接创建，常见3+1种方式
  三种构造方法：
    1. public String():创建一个空白字符串，不含有任何内容
    2. public String(char[ ]array):根据字符数组的内容，来创建对应的字符串
    3. public String(byte[ ] array):根据字节数组的内容，来创建对应的字符串
```java
    public static void main(String[] args) {
        String str = new String();
        System.out.println("First String is " + str);

        char[] arr = {'a', 'b', 'c'};
        String str2 = new String(arr);
        System.out.println("Second String is " + str2);

        byte[] arr_byte = {97, 98, 99};
        String str3 = new String(arr_byte);
        System.out.println("Third String is " + str3);

        String str4="abc";
        System.out.println("Forth String is "+str4);
```
 ### 二.字符串的常量池
 * 字符串常量池：程序中直接写上的双引号字符串，就在字符串常量池中
   **对于基本类型来说，==是进行数值的比较**；
   **对于引用类型来说，==是进行地址值的比较**。


```java
        String str1="abc";
        String str2="abc";
        char[]arr={'a','b','c'};
        String str3=new String(arr);
        System.out.println(str1==str2);		//ture
        System.out.println(str1==str3);		//false
        System.out.println(str2==str3);		//false
```
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/2020012218542176.png)
**双引号直接写的字符串在常量池中，new的不在池当中**
### 三.字符串的比较相关方法
* `==`是进行对象的地址值的比较，如果确实需要字符串的内容比较，可以使用两个方法
  `public boolean equals(Object obj):参数可以是任何对象`  
  `public boolean equalsIgnoreCase(String str):忽略大小写，进行内容比较`
  

```java
        String str1="abc";
        String str2="abc";
        char[]arr={'a','b','c'};
        String str3=new String(arr);
        if(str1.equals(str2))
            System.out.println("str1=str2");
        if(str1.equals(str3))
            System.out.println("str1=str3");
        if(str1.equals("abc"))
            System.out.println("str1=abc");
        if("abc".equals(str1))
            System.out.println("abc=str1");
```
  	![在这里插入图片描述](https://img-blog.csdnimg.cn/20200122191346564.png)
### 字符串的获取相关方法
String当中与获取相关的常用方法有：
1. public int Length():获取字符串中含有的字符个数
2. public String concat(String str):将当前字符串和参数字符串拼接成为返回值新的字符串
3. public char charAt(int index):获取指定索引位置的单个字符
4. public int indexOf(String str):查找参数字符串在本字符串中首次出现的索引位置，如果没有返回-1
### 字符串的截取方法
1. public String substring(int index):从参数位置一直到末尾，返回新字符串
2. public String substring(int begin,int end):从begin开始，一直到end结束，中间的字符串，左闭右开
### 字符串的转换方法
 1. public char[ ] toCharArray():将当前字符串拆分成为字符数组作为返回值
 2. public byte[ ] getBytes():获得当前字符串底层的字节数组
 3. public String replace(CharSequence oldString,CharSequence newString):将所有出现的老字符串替换成为新的字符串，返回替换后的结果新字符串`备注：CharSequence就是说可以接受字符串类型`
### 字符串的分割方法
分割字符串的方法;
public String[ ] split(String regex):根据参数的规则，将字符串切分为若干部分

```java
        String str="Yu,Feng,XU";
        String[]strString=str.split(",");
```
**注意事项**:split方法的参数其实是一个正则表达式，今后学习，今天要注意，如果按照英文句点`.`进行切分，必须写`\\.`两个反斜杠
## Arrays
Arrays是一个与数组相关的工具类，里面提供了大量静态方法，来实现数组的常见操作（**方便，静态方法不用new**）
常用方法
1. `public static String toString(数组)`
    功能：将参数数组变成字符串（按照默认格式，即[xx,xx,xx..]）
2. `public static void sort(数组)`
    功能：按照默认升序对数组元素进行排序
    备注：1. 如果是数值，sort默认按升序从小到大
    			2. 如果是字符串，sort默认按字母升序
    			3. 如果是自定义类型，**那么这个自定义类需要有Comparable或Comparator接口的支持
## 数学工具类Math
常用方法：
1. `public static double abc(double num)`
    abc是absolute的缩写，计算绝对值
 2. `public static double ceil(double num)`
      向上取整
 3. `public static double floor(double num)`
      向下取整
    4.`public static long round(double num)`
      四舍五入
 4. Math.PI
      代表近似的圆周率值
## Object类
### 重写equals方法(可以使用快捷键Alt+Insert)
因为java中`==`对于基本数据类型比较的是数值，对于引用数据类型比较的是地址值，所以对于自己创建的对象，继承了Object类的equals方法，需要对equals方法进行重写
```java
    @Override
    public boolean equals(Object obj) {
        //如果传递进来的是自身，直接返回true,提高程序效率
        if(obj==this)
            return true;
        //如果传空，直接返回false，提高程序效率
        if(obj==null)
            return false;
        //如果不是Student对象，直接返回false
        if (!(obj instanceof Student))
            return false;
        //向下转型，并进行比较
        Student stu = (Student) obj;
        if (this.age == stu.age && this.name == stu.name)
            return true;
        return false;
    }
```
* `Objects`类有一个equals方法，可以容忍空指针（注意不是Object类，是Objects类）
```java
    public static void main(String[] args) {
        Student stu=new Student("余丰旭",22);
        Student stu2=null;
        boolean b=Objects.equals(stu,stu2);
        System.out.println(b);
    }
```
因为其Objects.equals(**再次注意不是Object类是Objects类**）方法如下
```java
public static boolean equals(Object a,Object b){
	return (a==b)||(a!=null&&a.equals(b));
	}
```
### 重写toString方法（也可以快捷键）
```java
    @Override
    public String toString() {
        return name+":"+age+"岁";
    }
```
## Object类
位于`java.util.objects`
* JDK7中添加了Objects工具类，它有一些静态方法，这些方法是null-save(空指针安全的）或null-tolerant(容忍空指针的），用于计算对象的hashcode，返回对象的字符串表示形式，比较两个对象
* 在比较两个对象时，Object的equals方法容易抛出空指针异常，而Objects类中的equals方法就优化了这个问题
* 源码如下
```java
public static boolean equals(Object a,Object b){
return (a==b)||(a!=null&&a.equals(b));
```
## Date类
* `System.currentTimeMillis()`获取当前系统时间到1970年1月1日00：00：00（*英国格林威治时间*）经历了多少毫秒（long类型）
### 构造方法
1.无参构造
```java
        Date date=new Date();
        System.out.println(date);
        输出结果
        Mon Jan 27 19:47:17 CST 2020
```
2.带参构造（long类型）即毫秒值
```java
        Date date2=new Date(1580125811479L);
        System.out.println(date2);
        输出结果
        Mon Jan 27 19:50:11 CST 2020
```
### 常用方法
* `long getTime()`把日期转换为毫秒值
## DateFormat类
DateFormat类是**抽象类**，不能直接使用。所以需要常用子类`java.text.SimpleDateFormat`，这个类需要一个模式来指定格式化或解析的标准，构造方法为：
* `public SimpleDateFormat(String pattern)`:用给定的模式和默认语言环境的日期符号构造SimpleDateFormat

**格式规则**
| 标识字母（区分大小写） | 含义 |
| ---------------------- | ---- |
| y                      | 年   |
| M                      | 月   |
| d                      | 日   |
| H                      | 时   |
| m                      | 分   |
| s                      | 秒   |

> 备注：更详细的格式规则，请见API文档

写对应的模式，会把模式替换为对应的日期和时间：
`yyyy-MM-dd HH:mm:ss`
或
`yyyy年MM月dd日 HH时mm分ss秒`
其中连接符可以任意写
### format方法
传入date类型的参数，按照SimpleDateFormat中指定的模式输出字符串
```java
Date today=new Date();
SimpleDateFormat sdf=new SimpleDateFormat("yyyy年MM月dd日 HH时mm分ss秒");
System.out.println(sdf.format(today));
输出结果
2020年02月04日 11时55分41秒
```
### parse方法
把符合模式的字符串解析为Date日期
`public Date parse(String source)`throws ParseException
parse方法声明了一个异常叫ParseException解析异常，如果字符串和构造方法中的模式不一样，那么程序就会抛出异常，调用一个抛出了异常的方法，就必须处理这个异常，那么throws继续声明抛出这一个异常，要么try..catch自己处理这个异常（暂时用Alt+enter 让虚拟机自己处理）
```java
Date today=new Date();
SimpleDateFormat sdf=new SimpleDateFormat("yyyy年MM月dd日 HH时mm分ss秒");
Date day=sdf.parse("2020年02月04日 11时55分41秒");
System.out.println("parse方法将符合指定模式的字符串转换为Date类型数据:\n"+day);

输出结果：
parse方法将符合指定模式的字符串转换为Date类型数据:
Tue Feb 04 11:55:41 CST 2020
```
### 小练习：计算距出生多少天
```java
public class demo02 {
    public static void main(String[] args) throws ParseException {
        System.out.println("请按照格式输入出生日期: yyyy-MM-dd");
        Scanner sc=new Scanner(System.in);
        String birthString=sc.next();
        SimpleDateFormat sdf=new SimpleDateFormat("yyyy-MM-dd");
        Date birthday=sdf.parse(birthString);
        long birthdayMS=birthday.getTime();
        long nowMS=new Date().getTime();
        long distance=nowMS-birthdayMS;
        System.out.println("此人距出生已经历"+distance/1000/60/60/24+"天");
    }
}
```
## Calendar抽象类
很多Date类的方法已被Calendar类替代，java.util.Calendar类是一个抽象类，提供了很多操作日历字段的方法（YEAR,MONTH,DAY_OF_MONTH,HOUR)
Calendar类无法直接创建对象使用，其含有**静态方法**getInstance(),该方法返回一个Calendar类的子类对象
`static Calendar getInstance()`使用默认时区和语言环境获得一个日历
```java
使用了多态，用父类型接收子类对象
Calendar c=Calendar.getInstance();
```
### Calendar类常用方法
`public int get(int field)`：返回给定日历字段的值
`public void set(int field,int value)`：将给定日历字段设置为给定值
`public abstract void add(int field,int amout)`：根据日历的规则，为给定的日历字段添加或减去指定的时间量
`public Date getTime()`：转换为Date类对象
## System类
### 常用方法
1. `public static long currentTimeMillis()`：返回以毫秒值为单位的当前时间
2. `public static void arraycopy(Object src,int srcPos,Object dest,int destPos,int length)`：将数组中指定的数据拷贝到另一个数组中。
参数含义：src-源数组、srcPos-源数组中的起始位置、dest-目标数组、destPos-目标数组中的起始位置、length-复制的元素数量
## StringBuilder类
* 字符串是常量，它们的值在创建之后不能改变。字符串的底层是一个被final修饰的数组，不能改变，是一个常量。
因此会导致一个问题：拼接多个字符串时会产生大量字符串占用内存（如拼接a+b+c,内存中会产生a,b,c,ab,abc五个字符串
* **为了提高效率**，可以使用字符串缓冲区（可以看成长度可以变化的字符串），底层也是数组，但是没有被final修饰，可以改变长度。
**StringBuilder在内存中始终是一个数组，占用空间少，效率高；如果超出StringBuilder的容量，会自动扩容**
### 构造方法
* `public StringBuilder()`：构造一个空的StringBuilder容器
* `public StringBuilder(String str)`:构造一个StringBuilder容器，并将字符串添加进去
### 常用成员方法
* `public StringBuilder append(...)`：添加任意类型数据，**并返回当前对象自身**（可以链式编程）
* `public String toString()`：将当前StringBuilder对象转换为String对象
# 第五章 包装类
基本数据类型使用方便，效率高，但是**缺少对应的方法来操作这些数据**，故有时采用包装类。（除了char-Character和int-Integer,其他包装类都是首字母大写）
## 装箱与拆箱
* 装箱：基本类型转换为对应包装类对象
1. 通过构造方法(已过时）：
`Integer(int i)`
`Integer(String s)`：其中s必须时基本类型字符串，否则抛出异常 
2.静态方法：
`static Integer valueOf(int i)`：返回一个Integer实例
`static Integer valueOf(String s)`
* 拆箱：包装类对象转换为基本类型
成员方法：`int intValue()`
## 自动装箱与拆箱
从JDK1.5开始，基本类型和包装类的装箱与拆箱可以自动完成
如ArrayList<Integer\>中只能存储Integer包装类，但是可以直接写
```
ArrayList<Integer>list;
list.add(999);	//自动装箱
int a=list[0];		//自动拆箱
```
## 基本数据类型与字符串之间的转换
### 基本类型转字符串
总共三种方式
1. 基本数据类型+""即可
2. 使用包装类中的**静态方法**toString（参数)   *重载了Object的toString方法*
3. 使用String类中的**静态方法**valueOf(参数)
### String转基本数据类型
除了Character类以外，所有包装类都具有parseXxx静态方法，可以将字符串参数转换为对应的基本类型

# 第六章 接口
## 基础知识
* 接口就是一种**公共的规范标准**，如全中国的插座都一样。
只要符合标准就可以大家通用
* 代码中，接口就是**多个类的公共规范**
* 接口是一种引用数据类型，其中最重要的内容就是**抽象方法**
* 接口的格式

```java
public interface 接口名称{
接口内容
}
```
  备注：换成了关键字interface后，编译生成的字节码文件仍然是.java->.class
* 如果是JAVA7，则接口中可以包含的内容有：1.常量 2.抽象方法
* 如果是JAVA8，则：3.默认方法  4.静态方法
* 如果是JAVA9，则：5.私有方法
## 接口使用步骤
* 注意事项：
1.接口中的抽象方法，修饰符必须是两个固定的关键词：public abstract
2.**这两个关键字可以选择性地省略**，忽略任意一个或者两个一起省略都可以
* 接口使用步骤
1. 接口不能直接使用，必须有一个`实现类`(类似子类）来实现该接口
`public class 实现类名称 implements 接口名称{...}`
2. 接口的实现类必须覆盖重写（实现）接口中的所有抽象方法
3. 创建实现类的对象，进行使用
## 接口的默认方法定义
* 接口中的默认方法可以解决接口升级的问题（已经实现了原有接口的类，在接口添加了新的抽象类后，由于实现类必须实现接口所有的抽象方法，而不能再使用）使用带方法体的**默认方法**即可解决这个问题
 * 格式

```java
public default 返回值类型 方法名称（参数列表）{方法体}
```
* 注意事项
1.接口的默认方法，可以通过接口实现类对象直接调用
2.接口的默认方法，也可以被接口实现类进行覆盖重写
## 接口中的Static方法
* 从JAVA8开始，接口中允许定义Static方法
* 格式

```java
public static 返回值类型 方法名称（参数列表）{方法体}
```
* **不能通过接口实现类的对象来调用接口当中的静态方法**；正确用法：通过接口名称直接调用静态方法（静态只与类/接口，有关系）
## 接口中的私有方法
* 通过抽取一个共有方法，用来解决两个默认方法之间重复代码的问题，但这个共有方法不应该让实现类使用，应该是私有化的。
* 从JAVA9开始，接口中可以定义私有方法 
1.普通私有方法 ：解决多个**默认方法**之间重复代码问题

```java
private 返回值类型 方法名称（参数列表）{方法体}
```

2.静态私有方法 ：解决多个**静态方法**之间重复代码问题

```java
private static 返回值类型 方法名称（参数列表）{方法体}
```
## 接口的常量定义
* 接口中也可以定义“成员变量”，但必须加上`public static final`三个修饰符（可以省略，但是不写也照样是这样，不能写别的），从效果上来说，这就是接口的**常量***(final关键词修饰，说明不可改变）
* 格式
```java
public static final 常量名称=数据值;
```
* 接口中的常量，要**完全大写，下划线分割**，**必须赋初值**
* 使用时，直接用`接口名称.常量`即可
## 使用接口的注意事项
* 接口是没有静态代码块或构造方法的
* 一个类只有一个直接父类，但是一个类可以**同时实现多个接口**
格式：
```java
public class MyInterfaceImpl implements MyInterfaceA,MyInterfaceB{
	//覆盖重写所有抽象方法
	}
```
* 如果实现类所实现的多个接口中，存在重复的抽象方法，那么只需要覆盖重写一次即可。
* 如果实现类没有覆盖重写所有接口中的所有抽象方法，那么实现类必须是一个**抽象类**。
* 如果实现类所实现的多个接口中，存在着重复的默认方法，则实现类一定要对冲突的默认方法进行覆盖重写。
* **继承优先于接口实现**：一个类如果直接父类当中的方法和接口当中的默认方法产生了冲突，优先使用父类中的方法
## 接口之间的多继承
* 类与类之间是多继承的，直接父类只有一个
* 类与接口之间是多实现的，一个类可以实现多个接口
* 接口与接口之间是多继承的
注意事项：
1.多个父接口当中的抽象方法如果重复，没关系（因为抽象方法没有方法体）
2.多个父接口当中的默认方法如果重复，那么**子接口必须对重复的默认方法进行覆盖重写，`default`关键字不能省略**
***
# 第七章 多态
## 多态的基本概念
* 学生类继承人类，小明是一个学生对象，那么小明：既是一个学生，也是一个人，体现了**对象的多态性**
* 代码中体现多态性就是：**父类引用指向子类对象**
格式(左父右子）：
```java
父类名称 对象名=new 子类名称();
或者
接口名称 对象名称=new 实现类名称();
```
## 多态中成员方法的使用特点
* 看new的是谁，就优先用谁，没有则向上找
* 父子都有，优先用子

* 成员方法：编译看左，运行看右
* 成员变量：编译看左，运行还看左
## 使用多态的好处
等号左边形式统一
```java
		Worker teacher=new Teacher();
        Worker cooker=new Cooker();
        teacher.work();
        cooker.work(); 
```
## 对象的向上转型
对象的向上转型，其实就是多态写法
`父类名称 对象名=new 子类名称()`
含义：右侧创建一个子类对象，把它当作父类来看待使用
注意事项：**向上转型一定是安全的**

## 对象的向下转型
* 因为对象的向上转型有个缺点：无法通过父类引用指向子类对象的方式，调用子类特有方法
* 对象的向下转型，其实是**一个还原动作**，这样就可以调用子类独特方法
* 格式：`子类名称 对象名=（子类名称）父类对象`
* 注意事项：必须保证对象创建的时候本来就是猫，才能向下转型为猫
## instanceof的使用
cat instanceof Cat
# 第八章 Collection集合
集合是JAVA提供的一种容器，可以存储多个数据
与数组的区别：
1. 集合长度可变
2. 集合存储的是对象，且对象类型可以不一致
## 一、Collection集合

### 集合框架
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20200204170500694.png)
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20200204175909982.png)
### Collection常用功能
Collection是所有单列集合的父接口，因此在Collection中定义了单列集合（List和Set）通用的一些方法，这些方法可以操作**所有的单列集合**
* `public boolean add(E e)`:给定对象添加到集合中
* `public void clear()`：清空集合中所有元素
* `public boolean remove(E e)`：把给定的对象在当前集合中删除
* `public boolean contains(E e)`：判断当前集合中是否包含给定对象
* `public boolean isEmpty()`：判断集合是否为空
* `public int size()`：返回集合元素数量
* `public Object[ ] toArray()` ：把集合中的元素存储到数组中
## 二、Iterator迭代器
JDK中提供的专门用来**遍历集合**的接口（不是所有集合都有索引，即有些不能直接用for循环遍历）
**迭代**：即Collection集合元素通用的获取方式。取出元素前先判断集合中有没有元素，如果有则取出，再继续判断，一直到取完。这种取出方法的专业术语成为迭代
Iterator迭代器是一个接口，无法直接使用，需要接口的实现类对象。**其获取实现类的方式特殊**，Collection类中有一个方法叫iterator(),返回的就是Iterator的实现类对象
### 常用方法
`boolean hasNext()`：如果仍有元素可以迭代，返回true
`E next()`：返回迭代的下一个元素
```java
Collection<String>coll=new ArrayList<>();
coll.add("我爱你");
coll.add("中国");
coll.add("我爱你");
coll.add("塞北的雪");

Iterator<String>iter=coll.iterator();
while(iter.hasNext()){
    System.out.print(iter.next());
}
或者
for(Iterator<String>iter=coll.iterator();iter.hasNext();){
    System.out.print(iter.next());
}
```
## 三、增强for
增强for循环也称为**for each循环**，是JDK1.5以后出来的高级循环，**专门用来遍历数组和集合的**。它的内部原理其实是个Iterator迭代器，所以在遍历中不能对集合中的元素进行增删操作。
```java
//foreach遍历数组 （注意只能遍历，不能增删，因为其只是Iterator遍历的简化格式）
String[]arr={"我爱你","中国","亲爱的宝宝"};
for(String i:arr){
System.out.println(i);
}
//增强for循环遍历集合
Collection<Integer>coll=new ArrayList<>();
coll.add(1);
coll.add(2);
coll.add(3);
for(Integer i:coll){
System.out.println(i);
}
```
## 四、List接口
三个特点：1.有序 2.有索引 3.允许重复
### List接口中带索引的方法（特有）
1. `public void add(int index,E element)`：将指定元素插入到指定位置
2. `public E get(int index)`：获取指定位置上的元素
3. `public E remove(int index)`：删除指定位置上的元素，并返回该元素
4. `public E set(int index,E element)`：用指定元素替换指定位置，并返回原来的元素
### List的子类
#### 1.ArrayList
底层是数组，查询快，增删慢。增加元素时需要创建一个比原来数组大1的新数组，再调用System.arraycopy将原有数组拷贝到新数组，效率低。因此开发中，增删用的多不建议使用ArrayList
#### 2.LinkedList
特点：
1. 底层是链表结构：查询慢，增删快
2. 包含大量操作首尾元素方法

常用方法

1. `public void addFirst(E e)`
2. `public void addList(E e)`
3. `public E getFirst()`
4. `public E getLast()`
5. `public E removeFirst()`
6. `public E removeLast()`：**等效于add方法**
7. `public E pop()`：从此列表所表示的堆栈处弹出一个元素**等效于removeFirst**
8. `public void push(E e)`：将元素推入此列表所表示的堆栈 **等效于addFirst**
9. `public boolean isEmpty()`

>注意：获取元素时注意如果集合中没有元素，则会报错，需要用isEmpty先判断一下
#### 3.Vector集合
单线程的，速度慢，JDK1.2后被ArrayList取代。了解即可

 ## 五、Set接口
 特点：
 1. 不重复
 2. 没索引
### HashSet
* 特点：
1. 不允许重复
2. 没有索引
3. 是无序集合，存储和取出的顺序可能不同
4. 底层是一个哈希表结构（查询速度很快）
* 哈希值：是一个十进制的整数，由系统随机给出（就是对象的地址，是一个逻辑地址，不是实际地址）Object类中有一个方法，可以获取对象的哈希值
`int hashCode() `返回该对象的哈希值
* hashCode源码：`public native int hashCode();` 
**native**代表调用的是本地OS的方法
* String类重写了Object类的hashCode方法
 ### HashSet集合存储数据的结构——哈希表
 JDK1.8之前：哈希表=数组+链表
 JDK1.8之后：哈希表=数组+链表/红黑树（提高查询速度）超过8个用红黑树

 哈希表的特点：速度快
 ![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/2020020516101627.png)
### HashSet集合不重复原理
* **HashSet集合不允许存储重复元素的原理**：在Set集合调用add方法时，会先调用hashCode方法，如果没有发生哈希冲突则存储到集合中；若发生冲突，则再调用equals方法与冲突位置上的各元素比较，如果有相同的则不存储。
**前提**：存储的元素必须重写hashCode方法和equals方法
### HashSet存储自定义类型元素
给HashSet中存放自定义类型元素时，需要重写对象中的hashCode和equals方法，建立自己的比较方式，才能保证HashSet集合中的对象唯一
```java
 @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return age == person.age &&
                Objects.equals(name, person.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
```
### LinkedHashSet
LinkedHashSet继承了HashSet，底层是哈希表（数组+红黑树/链表）和一条链表（记录元素存储顺序），HashSet是无序的（不能保证存储和取出顺序一致），LinkedHashSet**额外实现了有序**（*存储和取出顺序一致*），**也不允许重复**

### 可变参数
使用前提：
1. 参数列表的类型已经确定，但参数个数不确定时
2. 一个方法的参数列表只能有一个可变参数
3. 如果参数有多个，**可变参数必须写在参数列表的末尾**

底层原理：可变参数底层是一个数组，根据传入参数个数会创建不同长度的数组
终极形式：`object...obj`
```java
    public static void main(String[] args) {
        System.out.println(add(1,2));
        System.out.println(add(1,2,3,4));
    }
    public static int add(int...arr){
        int sum=0;
        for(int i:arr){
            sum+=i;
        }
        return sum;
    }
```
## 六、Collections工具类
### 常用功能
`java.utils.Collections`是集合工具类，用来对集合进行操作。部分方法如下：

- `public static <T> boolean addAll(Collection<T> c, T... elements)  `:往集合中添加一些元素。
- `public static void shuffle(List<?> list) 打乱顺序`:打乱集合顺序。
- `public static <T> void sort(List<T> list)`:将集合中元素按照默认规则排序。（**使用前提**，必须实现Comparable，重写接口中的compareTo方法/*自己-目标是升序*/）
- `public static <T> void sort(List<T> list，Comparator<? super T> )`:将集合中元素按照指定规则排序。
**Comparable与Comparator的区别**
**Comparable**：即该类自身带有Comparable方法，可以实现本类的比较，**需要实现Comparable接口**，重写比较规则compareTo方法
**Comparator**：相当于找一个第三方裁判，**无需实现接口**；规则：o1-o2升序

例子：
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20200426113036323.png)
```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        Scanner sc=new Scanner(System.in);
        int n=sc.nextInt();
        ArrayList<String>list=new ArrayList<>();
        while(n-->0)
            list.add(sc.next());
        Collections.sort(list, new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                int a=Integer.parseInt(o1+o2);
                int b=Integer.parseInt(o2+o1);
                if(a>=b)
                    return -1;
                return 1;
            }
        });
        StringBuilder sb=new StringBuilder();
        for(String e:list)
            sb.append(e);
        System.out.println(sb);
    }
}
```

# 第九章 Map集合
## Map集合特点
1. Map集合是双列集合，一个元素包含两个值
2. key不可以重复，value可以重复
3. 一个key唯一对应一个value
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20200205204149398.png)
## 常用子类
### HashMap集合
特点：
1. 底层是哈希表（查询速度快）
JDK1.8之前：数组+单向链表  JDK1.8及以后：数组+单项链表/红黑树（8个+用红黑树）
2. HashMap是无序集合（存储和取出顺序有可能不同）
### LinkedHashMap集合
继承了HashMap集合
特点：
1. LinkedHashMap底层：哈希表+链表（保证迭代顺序）
2. 有序（存储和取出顺序一致）
### Hashtable集合
特点：
1. 不允许存储null键-null值
2. 线程安全的（同步的、单线程、速度慢）
HashMap是多线程的，非线程安全，不同步，速度快
3. 和Vector集合一样在JDK1.2后被取代（ArrayList，HashMap）
但是Hashtable的子类Properties依然活跃，它是唯一一个与IO流结合的集合

## Map接口中常用的方法
* `public V put(K key, V value)`:  把指定的键与指定的值添加到Map集合中。
如果key重复则替换value（返回被替换的值），不重复则添加（返回null）
* `public V remove(Object key)`: 把指定的键 所对应的键值对元素 在Map集合中删除，返回被删除元素的值。
* `public V get(Object key)` 根据指定的键，在Map集合中获取对应的值。
* `boolean containsKey(Object key)  ` 判断集合中是否包含指定的键。
* `public Set<K> keySet()`: 获取Map集合中所有的键，存储到Set集合中。
* `public Set<Map.Entry<K,V>> entrySet()`: 获取到Map集合中所有的键值对对象的集合(Set集合)。
```java
		Map<String,String> map=new HashMap<>();
		map.put("大伯","大妈");
		map.put("二伯","二妈");
		map.put("老爸","老妈");
		//利用keySet遍历Map
		Set<String> set = map.keySet();
		Iterator<String> iter = set.iterator();
		while(iter.hasNext()){
		   System.out.println(map.get(iter.next()));
		}
	    或者使用灵活版的foreach循环
        for(String i:map.keySet()){
            String value=map.get(i);
            System.out.println(i+"和"+value);
        }
```
## Entry（项）键值对对象
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20200205215016779.png)
## Map集合遍历键值对方式
键值对方式：即通过集合中每个键值对(Entry)对象，获取键值对(Entry)对象中的键与值。

操作步骤与图解：

1.  获取Map集合中，所有的键值对(Entry)对象，以Set集合形式返回。方法提示:`entrySet()`。

2.  遍历包含键值对(Entry)对象的Set集合，得到每一个键值对(Entry)对象。
3.  通过键值对(Entry)对象，获取Entry对象中的键与值。  方法提示:`getkey() getValue()`
```java
     Map<String,Integer>map=new HashMap<>();
     map.put("余丰旭",171);
     map.put("陈香玉",160);
     map.put("小小余",180);
     //遍历Map的第二种方法，使用Entey键值对对象
     //使用迭代器进行遍历
     Set<Map.Entry<String, Integer>> set = map.entrySet();
     Iterator<Map.Entry<String, Integer>> iter = set.iterator();
     while(iter.hasNext()){
         Map.Entry<String,Integer> entry=iter.next();
         String key=entry.getKey();
         Integer value=entry.getValue();
         System.out.println(key+"身高是"+value+"厘米");
     }
     //使用增强for循环
     for(Map.Entry<String,Integer> i:map.entrySet()){
         System.out.println(i.getKey()+"身高为"+i.getValue()+"cm");
     }
```
## HashMap存储自定义类型键值
Map集合保证key是唯一的：作为key的元素，必须重写hashCode方法和equals方法，以保证key唯一

# 第十章 泛型
当我们不知道使用什么数据类型的时候，我们可以使用泛型。**创建集合对象的时候**就会确定泛型的数据类型
## 一、泛型概述
* 创建集合对象，不使用泛型
**好处**：元素类型不必一致，默认类型是Object类
**弊端**：不安全，使用了多态导致子类独特内容被屏蔽，需要向下转型（循环中又无法统一向下转型）
* 创建集合对象，使用泛型
**好处**：
1.避免了类型转换的麻烦，存储的什么类型，取出来就是什么类型
2.把运行期异常（代码运行之后抛出的异常），提升到编译期。
*不使用泛型，需要向下转型，循环中会出现类型转换异常*

**弊端**：泛型是什么类型，就只能存储什么类型
## 二、定义和使用有泛型的类
**定义了带泛型的类，创建对象时不写则默认为Object类**
```java
public class One<E> {
    private E data;

    public One() {
    }

    public One(E data) {
        this.data = data;
    }

    public E getData() {
        return data;
    }

    public void setData(E data) {
        this.data = data;
    }
}
```
```java
public static void main(String[] args) {
	   One<String>one=new One<>("使用了泛型");
	   System.out.println(one.getData());
	   One<Integer>two=new One<>();
	   two.setData(12);
	   System.out.println(two.getData());
}
```
## 含有泛型的方法
含有泛型的方法，在调用方法时确定泛型的数据类型，传递什么类型的参数，泛型就是什么类型

格式：修饰符<泛型> 返回值类型 方法名（参数列表*(使用泛型)*）{方法体}
## 含有泛型的接口
 定义格式：
 ```
 修饰符 interface 接口名 <代表泛型的变量>{}
 ```
共有两种使用方式
* 接口的抽象方法以泛型为参数
* 接口的实现类的泛型跟着接口的泛型走
```
public class Two<E>implements MyInterface<E>
```
## 泛型通配符
用来表示暂时未知的类型
```java
public class Demo {
    public static void main(String[] args) {
        ArrayList<Integer>list1=new ArrayList<>();
        list1.add(1);
        list1.add(2);
        list1.add(3);
        ArrayList<String>list2=new ArrayList<>();
        list2.add("我爱你");
        list2.add("塞北的雪");
        traverseList(list1);
        traverseList(list2);
    }
    public static void traverseList(ArrayList<?>list){
        /*for (int i = 0; i < list.size(); i++) {
            System.out.println(list.get(i));
        }*/
        //使用迭代器遍历
        Iterator<?>iterator=list.iterator();
        while(iterator.hasNext()){
            System.out.println(iterator.next());
        }
    }
}
```
## 通配符高级使用——受限泛型
**泛型的上限**
* 格式：`类型名称 <? extends 类> 对象名称`
* 意义：`只能接收该类型及子类型`
**泛型的下限**
* 格式：`类型名称 <? super 类> 对象名称`
* 意义：`只能接收该类型及其父类型`
```java
此时参数只能接收Person类及其子类对象
public static void func(Collection<? extends Person> coll)
此时参数只能接收Person类及其父类对象
public static void func(Collection<? super Person> coll)
```[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-EKBUBlAi-1580906486394)(img\Collection与Map.bmp)]