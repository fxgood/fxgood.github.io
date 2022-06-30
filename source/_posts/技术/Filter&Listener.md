---
title: Filter&Listener
category: 技术
tag:
- web
date: 2020-5-12
---

# Filter基本概念

* JavaWeb三大组件：Servlet、listener、filter
* 当访问服务器资源时，过滤器可以将请求拦截下来，完成一些特殊功能

* 过滤器的作用
  * 一般用于完成通用的操作。如：登录验证、统一编码处理、敏感字符过滤....

<!--more-->

# 快速入门

1. 步骤：

   1. 定义一个类，实现接口Filter
   2. 复写方法
   3. 配置拦截路径
      1. web.xml
      2. 注解配置

2. 代码

   ```java
   @WebFilter("/*")   //拦截所有资源
   public class FilterDemo01 implements Filter {
   
       @Override
       public void init(FilterConfig filterConfig) throws ServletException {
   
       }
   
       @Override
       public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
           System.out.println("I am excuted");
           //放行
           filterChain.doFilter(servletRequest,servletResponse);
       }
   
       @Override
       public void destroy() {
   
       }
   }
   
   ```

   

3. 过滤器细节

   1. web.xml配置

      ```xml
         <filter>
            <filter-name>demo1</filter-name>
            <filter-class>com.hhu.web.filter.FilterDemo01</filter-class>
         </filter>
         <filter-mapping>
            <filter-name>demo1</filter-name>
            <url-pattern>/*</url-pattern>  <!--拦截所有资源-->
         </filter-mapping>
      ```

      

   2. 过滤器执行流程

      1. 执行过滤器
      2. 执行放行后的资源
      3. 回来执行过滤器放行代码下边的代码

   3. 过滤器生命周期方法

      1. 服务器启动后，会创建Filter对象，然后调用init方法，只调用一次。**用于加载资源**
      2. 服务器关闭后，Filter对象被销毁。如果服务器是正常关闭，则会执行destroy方法。**用于释放资源**
      3. doFilter方法每次请求被拦截资源时，会执行。**执行多次**

   4. 过滤器配置详解

      1. 拦截路径配置：
         1. 具体资源路径：`/index.jsp` 只有访问index.jsp资源时，过滤器才会被执行
         2. 拦截目录：`/user/*`  访问/user下的所有资源时，过滤器都会被执行
         3. 后缀名拦截：`*.jsp` 访问所有后缀名为jsp资源时，过滤器都会被执行
         4. 拦截所有资源: `/*`
      2. 拦截方式配置：资源被访问的方式
         * 注解配置（**注意配置了拦截方式后，原来的拦截路径配置就不能省略value=**）
           * 设置dispatcherTypes属性
             1. REQUEST：默认值。浏览器直接请i去资源
             2. FORWARD：转发访问资源
             3. （了解）INCLUDE：包含访问资源
             4. （了解）ERROR：错误跳转资源
             5. （了解）ASYNC：异步访问资源
         * web.xml配置
           * 设置\<dispatcher>\</dispatcher>标签即可

   5. 过滤器链（配置多个过滤器）

      * 执行顺序：如果有两个过滤器：1和2
        * 1
        * 2
        * 资源执行
        * 2
        * 1
      * 过滤器先后顺序问题
        * 注解配置：按照类名的字符串比较规则，值小的先执行
        * web.xml配置：谁定义在上边，谁先执行

# 案例：登录验证

* 需求

  * 对访问`用户信息列表展示`此案例的资源。验证其是否登录
  * 如果登录了，则直接放行
  * 如果没有，则跳转到登录也没面，提示“您尚未登录，请先登录“

* 图示

  [外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-CBRlZe4g-1589287434701)(Fliter&Listener.assets/image-20200510122521814.png)]



* 代码

  ```java
  /*
  * 完成登录验证的过滤器*/
  @WebFilter("/*")
  public class LoginFilter implements Filter {
      public void destroy() {
      }
  
      public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {
          //判断是否是登录相关的资源
          //0.强制转换
          HttpServletRequest request=(HttpServletRequest)req;
          //1.获取资源的请求路径
          String URI = request.getRequestURI();
          //2.判断是否包含登录相关资源路径,要注意排除掉css/js/图片/验证码资源
          if(URI.contains("/login.jsp")||URI.contains("/loginServlet")
                  ||URI.contains("/css/")||URI.contains("/fonts")
                  ||URI.contains("/js")||URI.contains("/checkCodeServlet")){
              //放行
              chain.doFilter(req, resp);
          }
          else{
              //不包含，验证用户是否登录
              //3.从session里获取user
              Object user = request.getSession().getAttribute("user");
              if(user!=null)
                  chain.doFilter(req, resp);
              else{
                  request.setAttribute("login_msg","您尚未登录，请登录");
                  request.getRequestDispatcher("/login.jsp").forward(request,resp);
              }
          }
      }
  
      public void init(FilterConfig config) throws ServletException {
  
      }
  
  }
  ```



# Listener 监听器

* 概念：web的三大组件
  * 事件监听机制
    * 事件：如点击按钮
    * 事件源：事件发生的地方。如按钮
    * 监听器：一个对象
    * 注册监听：将事件、事件源、监听器绑定在一起
      * 当事件源发生某个事件后，执行监听器代码
* **Listener用的少**
* ServletContextListener 接口：监听ServletContext对象的创建和销毁
  * 方法
    * void contextDestroyed(ServletContextEvent sce)
      * ServletContext对象被销毁之前会调用该方法
    * void contextInitialized(ServletContextEvent sce)
      * ServletContext对象创建后会调用该方法