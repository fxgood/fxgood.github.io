---
title: servlet
date: 2020-05-12
category: 技术
tag:
- web
excerpt: 记录servlet的一点基础知识。
---

# 概念

运行在服务器端的小程序

* Servlet就是一个接口，定义了java类被浏览器访问（tomcat识别）的规则
* 将来我们自定义一个类，实现Servlet接口，复写方法
  [外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-6JtH1J5q-1589287306416)(servlet.assets/image-20200504214004662.png)]

# 快速入门

1. 创建一个javaEE项目

2. 定义一个类，实现Servlet接口

3. 实现接口中的五个抽象方法

4. 配置Servlet

   * 在web.xml中配置

   * ```html
     <!--配置Servlet-->
         <servlet>
             <servlet-name>demo01</servlet-name>
             <servlet-class>com.hhu.web.servlet.servletDemo01</servlet-class>
         </servlet>
         <!--配置servlet和url的映射-->
         <servlet-mapping>
             <servlet-name>demo01</servlet-name>
             <url-pattern>/demo01</url-pattern>
         </servlet-mapping>
     ```

# Servlet执行原理

1. 当服务器接收到客户端浏览器的请求之后，会解析请求url，获取访问的Servlet的资源路径。
2. 查找web.xml文件，是否有对应的<url-pattern>标签体内容
3. 如果有，则会找到对应的<servlet-class>全类名
4. tomcat会将字节码文件加载进内存，并且创建其对象
5. 调用其方法

# Servlet中的生命周期（方法）

 	1. init方法|
 	 * 初始化方法
 	 * 在Servlet被创建时执行。只会执行一次
 	2. getServletConfig方法
 	* 获取ServletConfig对象
 	3. service方法
 	 * 提供服务方法
 	 * 每次Servlet被访问时，执行。执行多次

3. getServletInfo方法
   * 获取Servlet的一些信息：版本，作者等

4. destroy方法
   * 销毁方法
   * 在服务器正常关闭的时候执行，执行一次

* Servlet的生命周期：
  * 被创建：执行inti方法，执行一次

    * Servlet什么时候被创建？

      * 默认情况下，第一次被访问时，Servlet被创建

      * 可以在servlet标签下配置执行Servlet的创建时机

        ```html
        <!--配置servlet创建时机
                    负数：第一次被调用时创建
                    自然数：服务器启动时创建-->
                <load-on-startup>5</load-on-startup>
        ```

      * Servlet的init方法，只执行一次，说明一个Servlet在内存中只存在一个对象，Servlet是单例的

        * 多个用户同时访问时，可能存在线程安全问题
        * 解决： **尽量不要在Servlet中定义成员变量**，即使定义了也不要对其修改值

  * 提供服务：执行service方法，执行多次

  * 被销毁：执行destroy方法，只执行一次

    * destroy方法在servlet被销毁之前执行，一般用于释放资源

***

# Servlet3.0

* 好处：

  * 支持注解配置。可以不需要web.xml

* 步骤

  1. 创建javaEE项目，选择Servlet版本3.0以上，可以不创建web.xml

  2. 定义一个类实现Servlet接口

  3. 复写方法

  4. 在类上使用@WebServlet注解，进行配置

     1. ```
        @WebServlet("/资源路径（自定义）")
        ```

# IDEA与Tomcat的相关配置

1. IDEA会为每个tomcat部署的项目单独建立一份配置文件
2. 工作空间项目 和 tomcat部署的web项目
   * tomcat真正访问的是"tomcat部署的项目"，其对应着”工作空间项目”的web目录下的所有资源
   * WEB-INF目录下的资源不能被浏览器直接访问
     * **不要直接把资源放到WEB-INF下，浏览器无法访问**
   * 断点调试：使用”小虫子“debug启动

# Servlet体系结构

爷父孙三者

* Servlet接口
  * GenericServlet 抽象类
    * HttpServlet 抽象类

* GenericServlet：将Servlet接口中其他方法做了默认空实现，只将service（）方法作为抽象
  * 将来自定义类时，可以**继承**GenericServlet类，实现service（）方法。其他方法有需要也可以复写
  * ( **推荐使用**)HttpServlet ：对于http协议的一种封装，简化操作
  * 定义类继承HttpServlet
  * 复写doGet/doPost方法
  * （HttpServlet已经对service方法进行了复写，在service内部对post和get方法做了判断，各自调用doPost和doGet方法，故只需对doGet/doPost方法做实现即可）

# Servlet 相关配置

1. urlpartten：Servlet访问路径
   1. 一个Servlet可以定义多个访问路径
      `@WebServlet({"/demo03","/03"})`
   2. 路径定义规则：
      1. /xxx
      2. /xxx/xxx：多层路径，目录结构
      3. *.自定义拓展名   注意前面不要加杠 （\*通配符）