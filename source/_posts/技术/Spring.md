---
title: Spring
date: 2020-05-06
category: 技术
excerpt: 记录Spring的基础与原理学习心得，为后续学习SSM、Spring Boot、Spring Cloud做准备。 
---

# Spring概念

1. Spring是轻量级的开源的JavaEE框架
   - 轻量级：引入Jar包少，体积小，可以独立进行使用
2. Spring出现的目的：解决企业应用开发的复杂性
3. Spring有两大核心特性：
   - IOC：**控制反转，把创建对象过程交给Spring进行管理**
   - AOP：面相切面编程，在不改变源代码的情况下扩展功能（增强了可扩展性）
4. Spring特点
   1. 方便解耦、简化开发
   2. 支持AOP编程
   3. 方便程序的测试（整合了Junit4）
   4. 方便和其他框架进行整合
   5. 方便进行事务操作
   6. 降低API开发难度（比如对JDBC进行了封装）
   7. Spring的源码，是Java学习的经典范例
5. Spring的组成部分

![image-20211102104630482](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211102104630482.png)

# IOC容器

## 什么是IOC

1. 控制反转，把对象创建和对象之间的调用过程，交给Spring进行管理

2. 使用IOC目的：将对象创建和对象之间的调用，都统一交给Spring进行管理。简化了代码开发过程，降低了耦合度。如果以原始方式构建代码，效果如下： 
   <img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211102141123873.png" alt="image-20211102141123873" style="zoom: 50%;" />

两个类之间耦合度过高，如果UserDao发生改变（修改方法，增加方法等扩展行为），则会牵一发动全身，不利于程序的扩展性。

我们追求的是程序的**高内聚、低耦合**。

```
SpringIOC的好处：全称是Inversion of Control 简称 IoC，控制反转。是指程序中对象的获取方式发生反转，由最初的new方式创建，转为由框架创建、注入，这样可以降低对象之间的耦合度。

IoC（Inversion of Control，控制反转）也称为依赖注入（Dependency Injection），作为Spring的一个核心思想，是一种设计对象之间依赖关系的原则及其相关技术。先来看看字面上怎么来解释：当一个对象创建时，它所依赖的对象由外部传递给它，而非自己去创建所依赖的对象（比如通过new操作）。因此，也可以说在对象如何获取它的依赖对象这件事情上，控制权反转了。这便不难理解控制反转和依赖注入这两个名字的由来了。
```

## IOC底层原理

1. xml解析
2. 工厂模式
   - 工厂模式出现的目的就是为了实现低耦合
     <img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211102141722190.png" alt="image-20211102141722190" style="zoom: 50%;" />
   - 并没有将耦合度降低到最低，工厂类和UserService仍然具有较强的耦合度
3. 反射
   - 反射：`.java`文件编译成`.class`字节码文件后，获取字节码文件，以实现对类的控制

![image-20211102144057399](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211102144057399.png)

## IOC接口（BeanFactory）

1. IOC思想基于IOC容器完成，IOC容器底层就是对象工厂
2. Spring提供IOC容器实现两种方式：（两个接口）
   1. BeanFactory：IOC容器基本实现，是Spring内部使用的接口，一般不提供给开发人员使用。
      - 加载配置文件时，不会创建对象；在获取（使用）对象时，才会创建对象
   2. ApplicationContext：BeanFactory接口的子接口，提供了更多更强大的功能，一般面向开发人员进行使用
      - 加载配置文件时，就创建对象
      - 将耗时耗资源的过程在服务器启动的时候就完成，执行过程中直接用就可以了

3. ApplicationContext实现类

   <img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211102154334695.png" alt="image-20211102154334695" style="zoom:50%;"   />

## IOC操作Bean管理
- 什么是Bean管理
  1. Spring创建对象
  2. Spring进行属性注入
- 两种实现方式
  - 基于XML
  - 基于注解

### 两种类型bean

1.Spring有两种类型bean，一种是普通bean，一种是工厂bean（FactoryBean）

- 普通bean：在配置文件中定义的bean类型，就是返回类型
- 工厂bean：在配置文件中定义的bean类型可以和返回类型不同
  1. 创建类，让这个类作为工厂bean，实现FactoryBean接口
  2. 实现接口里的方法，让实现的方法中定义返回的bean类型

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211103223515546.png" alt="image-20211103223515546" style="zoom:80%;" />


<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211103223548776.png" alt="image-20211103223548776" style="zoom:80%;"  />


> 注意，context.getBean的第二个参数用的不是MyBean.class,而是Person.class

### bean作用域

1. 在Spring中，设置创建bean实例是单实例还是多实例（如何验证：连续两次使用 `new ClassPathXmlApplicationContext()返回对象，看地址值是否相同`）

2. bean中有个属性，有个`scope`属性，用于设置单实例还是多实例（默认是单实例）
   - `singleton`：单实例，加载spring配置文件时(`new ClassPathXmlApplicationContext()`)，就会创建单实例对象
   - `prototype`：多实例，在调用getBean方法时创建多实例对象
   - `request`：了解，每次创建对象会放入request中
   - `session`：了解，每次创建对象会放入session中

### bean生命周期

> 生命周期：从对象创建到销毁的过程

1. 通过构造器创建bean实例（无参构造）
2. 为bean的属性设置值和对其他bean引用（调用set方法）
3. 调用bean的初始化方法（bean标签`init-method`指定方法名）
4. bean可以使用（对象获取到了）
5. 当容器关闭的时候，调用bean的销毁方法（bean标签`destroy-method`指定方法名，使用`ClassPathXmlApplicationContext`接口的close方法会自动调用指定的方法）

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211104210333558.png" alt="image-20211104210333558" style="zoom:50%;" />

（注意，`ClassPathXmlApplicationContext`是`ApplicationContext`的子接口，其中close是子接口的特有方法）

### bean的后置处理器 

加上后置处理器以后，bean的生命周期由五步变成七步

1. 通过构造器创建bean实例（无参构造）
2. 为bean的属性设置值和对其他bean引用（调用set方法）
3. **把bean实例传递到bean后置处理器方法**`postProcessBeforeInitialization`
4. 调用bean的初始化方法（bean标签`init-method`指定方法名）、
5. **把bean实例传递给bean后置处理器的方法**`postProcessAfterInitialization`
6. bean可以使用（对象获取到了）
7. 当容器关闭的时候，调用bean的销毁方法（bean标签`destroy-method`指定方法名，使用`ClassPathXmlApplicationContext`接口的close方法会自动调用指定的方法）

![image-20211104211134028](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211104211134028.png)

**配置后置处理器后，同一个xml中所有bean都添加了后置处理器**。（如下，orders对象就被自动配置了后置处理器）

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211104211418808.png" alt="image-20211104211418808" style="zoom:50%;"   />

### IOC操作Bean管理（基于xml）

#### 基于xml方式创建对象

- 在spring配置文件中，使用bean标签，标签里边添加对应属性，就可以实现对象创建
- 在bean标签中有很多属性
  - id属性：唯一标识
  - class属性：类全路径
- 创建对象时，默认执行无参构造函数完成对象创建

#### 基于xml方式注入属性

- DI：依赖注入，就是注入属性(需要先创建对象)

> IOC与 DI的区别，DI是IOC的一种实现方式

（1）基于setter的注入

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211102162813308.png" alt="image-20211102162813308" style="zoom:80%;"   />

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211102162856901.png" alt="image-20211102162856901" style="zoom:80%;"   />

（2）基于构造函数的注入

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211102164607391.png" alt="image-20211102164607391" style="zoom:80%;"   />

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211102164641632.png" alt="image-20211102164641632" style="zoom:80%;"   />

(3) p名称空间注入（了解）

可以**简化基于xml配置方式**。(但仍然是基于setter的注入)

1. 在配置文件中添加p名称空间
2. 进行属性注入，在bean标签里进行操作

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211102194507329.png" alt="image-20211102194507329" style="zoom:80%;"   />

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211102194532747.png" alt="image-20211102194532747" style="zoom:80%;"   />

（4）注入null或特殊符号
<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211102200122222.png" alt="image-20211102200122222" style="zoom:80%;"  />

#### 注入外部bean与级联赋值

需要使用setter来进行注入

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211102211636236.png" alt="image-20211102211636236" style="zoom:67%;"   />

#### 注入内部bean

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211102213109793.png" alt="image-20211102213109793" style="zoom:100%;"   />

#### 级联赋值

即使用外部bean注入属性（使用ref），外部的bean中也已经注入了属性

方式一：

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211102215137539.png" alt="image-20211102215137539" style="zoom:80%;"   />

方式二：

**注意：在Employee类中必须为成员Department dpt设置Getter**，注意是getter，否则`dept.name`会报错

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211102220024846.png" alt="image-20211102220024846" style="zoom:80%;"   />

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211102220228560.png" alt="image-20211102220228560" style="zoom:80%;"   />

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211102220248851.png" alt="image-20211102220248851" style="zoom:80%;"   />

#### 注入集合属性

1.数组

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211103212054364.png" alt="image-20211103212054364" style="zoom:67%;"   />

2.List

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211103212509728.png" alt="image-20211103212509728" style="zoom:80%;"   />

3.Map

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211103212625460.png" alt="image-20211103212625460" style="zoom:80%;"   />

4.Set

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211103212643009.png" alt="image-20211103212643009" style="zoom:80%;"   />

> 注：name随便取的，和类中成员的名字相同即可

5.注入集合的元素是对象

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211103214942019.png" alt="image-20211103214942019" style="zoom:67%;"   />

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211103215007067.png" alt="image-20211103215007067" style="zoom:67%;"   />

**进阶：将集合注入部分抽取出来，用于复用**

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211103220531339.png" alt="image-20211103220531339" style="zoom:80%;"   />

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211103220628060.png" alt="image-20211103220628060" style="zoom:80%;"   />



#### xml自动装配

自动装配：根据指定装配规则（属性名称或者属性类型），Spring自动将匹配的属性值进行注入

实现方式：增加`autowire`属性，值可以选择`byname` or `bytype`

**根据属性名称进行自动装配**

xml中用于自动装配的bean的`id`需要和属性名相同

**根据属性类型进行自动装配**

相同类型的bean不能定义多个（会报错，因为会冲突）

> xml自动装配实际用的很少，一般都用注解方式进行实现

#### 引入外部属性文件

首先导入druid连接池jar包（在projectStructure里导入）

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211110202453165.png" alt="image-20211110202453165" style="zoom:80%;"   />

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211110202518453.png" alt="image-20211110202518453" style="zoom:80%;"   />



### IOC操作Bean管理（基于注解）

使用注解的目的：简化xml配置

> 

#### 基于注解方式创建对象

**四个注解**

1. `@Component`
2. `@Service`
3. `@Controller`
4. `@Repository`

> 注：上面四个注解功能一样，都可以用来创建bean实例。



**基于注解创建对象过程**

1. 引入依赖`spring-aop-5.2.6.RELEASE.jar`
2. 开启组件扫描
   - 需要在xml文件中添加`xmlns:context="http://www.springframework.org/schema/context`
   - `http://www.springframework.org/schema/context  http://www.springframework.org/schema/context/spring-context.xsd`
3. 创建类，在类上添加创建对象注解
   - value属性值可以省略，默认值是类名称，首字母小写

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211110212257345.png" alt="image-20211110212257345" style="zoom:80%;"   />

其中扫描包的过滤器可以自定义，见下图

（use-default-filters='false' 表示不使用默认的filter，默认会扫描指定包下的所有类）

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211110213353812.png" alt="image-20211110213353812" style="zoom: 67%;"   />

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211110212334044.png" alt="image-20211110212334044" style="zoom:100%;"   />

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211110212403889.png" alt="image-20211110212403889" style="zoom:80%;"   />



#### 基于注解方式注入属性

**相关注解**

1. `@AutoWired`：根据属性**类型**进行自动注入

   - 注意不需要添加set方法哦

   - <img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211110214906284.png" alt="image-20211110214906284" style="zoom:67%;"   />

2. `@Qualifier`：根据属性**名称**进行自动注入

   - 需要配合autowired一起使用
   - <img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211110214954465.png" alt="image-20211110214954465" style="zoom:80%;"   />

3. `@Resource`：根据属性**类型/名称**进行自动注入

   - 它是`javax.annotation`扩展包下的，不是spring下的

   - <img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211110215137364.png" alt="image-20211110215137364" style="zoom:80%;"   />

4. `@Value`：注入**普通数据类型**（上面三种都是注入对象类型）
   - <img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211110215435271.png" alt="image-20211110215435271" style="zoom:80%;"   />

#### 纯注解开发

1. 创建配置类，替代xml的配置文件
2. 测试时，有所不同

![image-20211124101158905](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211124101158905.png)

![image-20211124101731004](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211124101731004.png)

![image-20211124101210026](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211124101210026.png)

> 实际开发中，会使用SpringBoot来完成这些工作，SpringBoot本质就是Spring

# AOP

## 概念

1. 面相切面编程，利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。
2. 通俗描述：不通过修改源代码方式，在主干功能里面添加新功能。
3. 使用登录案例说明AOP：

![image-20211125140021182](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211125140021182.png)

## 底层原理

原理：使用动态代理，在不改变原有代码基础上增强功能

1. 有接口

   - 使用JDK动态代理

   - ```java
     public interface Animal {
         public void speak();
     
         public int calculate(int a,int b);
     }
     
     public class Cat implements Animal{
     
         @Override
         public void speak() {
             System.out.println("喵喵喵");
         }
     
         @Override
         public int calculate(int a, int b) {
             System.out.println("正在计算...");
             return a+b;
         }
     }
     
     public class Test {
         public static void main(String[] args) {
             Class[]interfaces={Animal.class};
             Object cat=new Cat();
             Animal animal=(Animal)Proxy.newProxyInstance(Test.class.getClassLoader(),interfaces,new AnimalProxy(cat));
             animal.speak();
             animal.calculate(1,2);
         }
     }
     
     class AnimalProxy implements InvocationHandler{
         private Object obj; //将要代理的对象传入
         public AnimalProxy(Object obj){
             this.obj=obj;
         }
     
         @Override
         public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
             //方法执行前
             if(method.getName()=="speak"){
                 System.out.println("叫之前...");
             }
             //执行原有的方法
             Object res = method.invoke(obj, args);
     
             //方法执行后
             if(method.getName()=="speak"){
                 System.out.println("叫之后...");
             }
             else if(method.getName()=="calculate"){
                 System.out.println("计算结果是"+res);
             }
             return res;
         }
     }
     ```

   - ![image-20211130143414516](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211130143414516.png)

2. 无接口

   - 使用GCLIB动态代理
   - ![image-20211130143441043](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211130143441043.png)

## AOP术语

**连接点**

- 类中**可以**被增强的方法，就被称为连接点

**切入点**

- 实际真正被增强的方法，成为切入点

**通知（增强）**

- 实际增强的逻辑部分成为通知（增强）
- 通知有多重类型
  - 前置通知
  - 后置通知
  - 环绕通知：在被增强的方法的前后都有执行
  - 异常通知：当被增强的方法出现异常时，执行
  - 最终通知：类似finally

**切面**

- 把通知应用到切入点的过程，就称为切面（切面是一个动作）



## AOP操作

1. Spring框架一般都是基于AspectJ实现AOP操作
   - 什么是AspectJ：它不是Spring的组成部分，是一个独立的AOP框架，一般把AspectJ和Spring框架一起使用，进行AOP操作
2. 基于AspectJ实现AOP操作
   - 基于xml配置文件实现
   - 基于注解实现（实际使用）
3. 在项目工程里引入AOP相关依赖

![image-20211202105505615](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211202105505615.png)

> 导入lib后，需要在project struct中Add。 各种依赖在百度网盘-编程相关-各种软件包里有

4. 切入点表达式
   - 作用：知道对哪个类中的哪个方法进行增强
   - 语法结构
   ![image-20211202110247596](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211202110247596.png)
   ![image-20211202110302698](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211202110302698.png)

5. 基本流程

(1)创建类，定义方法

(2)创建增强类，编写增强逻辑

(3)在增强类上添加注解`@Aspect`

(4)在spring配置文件中开启生成代理对象  

(5)配置不同类型的通知

![image-20211202112443608](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211202112443608.png)

![image-20211202112456184](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211202112456184.png)

![image-20211202112521015](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211202112521015.png)

![image-20211202112529858](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20211202112529858.png)



# JdbcTemplate

# 事务管理

# Spring5新特性

