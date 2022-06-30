---
title: cookie&session&jsp
category: 技术
tag:
- web
date: 2020-5-12
---

# 会话技术

1. 会话：一次会话中包含多次请求和响应
   * 一次会话：浏览器第一次给服务器资源发送请求，会话建立，直到有一方断开为止
   * 功能：**在一次会话的范围的多次请求间内共享数据**
   * 方式
     * 客户端会话技术:cookie
       * 数据存在客户端
     * 服务器端会话技术:session
       * 数据存在服务器端

<!--more-->

***

# Cookie

1. 概念：客户端会话技术，将数据保存到客户端

## 快速入门

* 使用步骤
  1. 创建（API）Cookie对象，绑定数据
     * new Cookie(String name,String value)
  2. 发送Cookie对象
     * response.addCookie(Cookie cookie)
  3. 获取Cookie，拿到数据
     * Cookie[] request.getCookies()

## Cookie实现原理

基于响应头set-Cookie和请求头cookie实现

[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-AAhdofVS-1589287359257)(会话技术cookie&session&JSP.assets/image-20200505183236228.png)]

## Cookie的细节

1. 一次可不可以发送多个Cookie？
   * 可以
   * 可以创建多个cookie对象，使用response调用多次addCookie发送cookie即可
2. Cookie在浏览器中保存多长时间？
   * 默认情况下，浏览器关闭后，cookie数据被销毁
   * 持久化存储：
     * setMaxAge(int seconds)
       * 正数：持久化将Cookie数据写到硬盘的文件中。代表cookie存活时间
       * 负数：默认情况
       * 0：删除cookie信息
3. cookie能不能存中文？
   * 在tomcat8之前，cookie中不能直接存储中文数据
     * 需要将中文数据转码，一般采用URL编码（如%E3）
   * 在tomcat8及之后，cookie支持中文数据。特殊字符还是不支持（如空格），建议使用URL编码存储，URL解码
4. cookie获取的范围是多大？
   * 假设在**一个tomcat服务器**中，部署了**多个web项目**，那么在这些web项目中，cookie能不能共享？
     * 默认情况下，cookie不能共享
     * setPath(String path)：设置cookie的获取范围。默认情况下，设置当前的虚拟目录
     * 设置setPath("/") 即可实现多个web项目之间的cookie共享 
   * 不同的tomcat服务器间cookie共享问题？
     * setDomain(String path)：如果设置一级域名相同，那么多个服务器之间cookie之间可以共享
       * setDomain(".baidu.com")，那么tieba.baidu.com和news.baidu.com中cookie可以共享

## Cookie的特点和作用

1. Cookie存储数据在客户端浏览器，不安全
2. 浏览器对于单个cookie大小有限制，以及同一个域名下的总cookie数量有限制（20个以内）

* 作用
  1. cookie一般用于存储少量不敏感数据
  2. **在不登录的情况下**，完成服务器对客户端的身份识别

## 案例：记住上一次访问的时间

1. 需求
   1. 访问一个Servlet，如果是第一次访问，则提示：您好，欢迎您首次访问
   2. 如果不是第一次访问，则提示：欢迎回来，您上一次访问时间为：显示时间字符串
2. 分析：
   1. 可以用Cookie完成
   2. 在服务器的Servlet中判断是否有一个名为lastTime的cookie
      1. 有：不是第一次访问
         1. 响应数据：您好，欢迎回来，您上次访问的时间为：xxxx
         2. 写回cookie
      2. 没有：是第一次访问
         1. 响应数据：您好，欢迎首次访问
         2. 写回Cookie：lastTime=xxx

3. 代码

```java
@WebServlet("/rememberLastTimeAccess")
public class rememberLastTimeAccess extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //设置响应编码
        response.setContentType("text/html;charset=utf-8");
        //获取cookie
        Cookie[] cookies = request.getCookies();
        boolean flag=true;
        if(cookies!=null&&cookies.length>0){
            for(Cookie c:cookies){
                if("lastTime".equals(c.getName())){
                    flag=false;
                    //有该cookie表示不是第一次访问
                    String value = c.getValue();
                    String decode = URLDecoder.decode(value,"utf-8");
                    response.getWriter().write("<h1>欢迎回来，您上次的访问时间："+decode+"</h1>");
                    //获取当前时间的字符串，重新设置cookie的值
                    Date date=new Date();
                    String time = new SimpleDateFormat("yyyy年mm月dd日 HH:mm:ss").format(date);
                    //string中有空格，cookie不支持，故进行URL编码
                    String encode = URLEncoder.encode(time, "utf-8");
                    c.setValue(encode);
                    //设置cookie存活时间
                    c.setMaxAge(3600*24*30); //存储一个月
                    response.addCookie(c);//会覆盖原来的值
                    break;
                }
            }
        }
        if(cookies==null||cookies.length==0||flag) {
            response.getWriter().write("<h1>欢迎您，首次访问！</h1>");
            Date date=new Date();
            String time = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss").format(date);
            String encode = URLEncoder.encode(time, "utf-8");
            Cookie cookie=new Cookie("lastTime",encode);
            cookie.setMaxAge(3600*24*30);
            response.addCookie(cookie);
        }

    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request, response);
    }
}
```



# Session 

1. 概念：服务器端会话技术，在一次会话的多次请求间共享数据，将数据保存在服务器端的对象中（HttpSession）

## 快速入门

* HttpSession对象
  * 获取session对象
    * request.getSession()
  * 使用
    * getAttribute(String name)
    * setAttribute(String name,Object value)
    * removeAttribute()

## 原理

* 服务器如何确保在一次会话范围内，多次获取的session对象是同一个？
  * Session是依赖于Cookie的（相当于把数据存在服务器，把这份数据的id通过cookie存在客户端浏览器）

[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-zzUpGT0d-1589287359260)(会话技术cookie&session&JSP.assets/image-20200506102119056.png)]

## 细节

1. 当客户端关闭后，服务器不关闭，两次session是否为同一个？

   * 默认情况下：不是（请求头中没有cookie记录）

   * 如果需要相同，则可以创建Cookie，键设置为JSESSIONID，值为获取到的session的id，设置最大存活时间，让cookie持久化保存

     ```java
     Cookie cookie=new Cookie("JSESSIONID",session.getId());
     response.addCookie(cookie);
     ```

     

2. 客户端不关闭，服务器关闭后，两次获取的session是同一个吗？

   * 不是同一个，但是要确保数据不丢失（Tomcat已帮我们自动完成，必须用本地的tomcat服务器启动才能实现，idea中部署tomcat服务器无法完成）
     * Session的钝化
       * 在服务器正常关闭之前，将Session对象序列化到硬盘上
     * Session的活化
       * 在服务器启动后将session文件转化为内存中的Session对象即可

3. Session的默认失效时间？

   * 服务器关闭
   * session对象调用invalidate（）
   * session默认失效时间是30分钟
     * 可以在tomcat/conf/web.xml的 \<session-config>中设置

## 特点

1. session用于存储一次会话的多次请求间的数据，存储在服务器端
2. session可以存储任意类型，任意大小的数据

* session与cookie的区别
  * session在服务器端存储数据，cookie存在客户端
  * session可以存储任意数据类型，没有数据大小限制，cookie只能存储字符串，有大小限制
  * session比较安全，cookie相对不安全

## 案例：验证码

# JSP

## 入门学习

1. 概念：Java Server Pages：java服务器端页面
   * 可以理解为：一个特殊的页面，其中既可以指定定义html标签，又可以定义java代码
   * 用于简化书写
2. 原理
   [外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-7V2NgSrk-1589287359261)(会话技术cookie&session&JSP.assets/image-20200505214202822.png)]

* 本质：**JSP本质是一个Servlet**

3. JSP的脚本：JSP定义Java代码的方式
   1. `<%java代码%>`：定义的java代码在Service方法中。service方法中可以定义什么，该脚本中就可以定义什么
   2. `<%! java代码%>`：定义成员（成员变量，成员方法）
   3. `<%=java代码%>`：会将内容输出到页面中
4. jsp的内置对象
   * 在jsp页面中不需要获取和创建，可以直接使用的对象
   * jsp一共有9个内置对象
   * 常用的3个
     * request
     * response
     * out：字符输出流对象，可以将数据输出到页面上。和response.getWriter()类似
       * tomcat服务器真正给客户端做出响应之前，会先找到response缓冲区数据，再找out缓冲区数据
       * response.getWriter().write()数据输出永远在out.write()之前

5. 案例：改造cookie案例

   ```java
   <%@ page import="java.net.URLDecoder" %>
   <%@ page import="java.util.Date" %>
   <%@ page import="java.text.SimpleDateFormat" %>
   <%@ page import="java.net.URLEncoder" %><
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
     <head>
       <title>hello</title>
     </head>
     <body>
     <%
       //获取cookie
       Cookie[] cookies = request.getCookies();
       boolean flag=true;
       if(cookies!=null&&cookies.length>0){
         for(Cookie c:cookies){
           if("lastTime".equals(c.getName())){
             flag=false;
             //有该cookie表示不是第一次访问
             String value = c.getValue();
             String decode = URLDecoder.decode(value,"utf-8");%>
     <h1>欢迎回来，您上次的访问时间：<%=decode%></h1>
     <%
   
             //获取当前时间的字符串，重新设置cookie的值
             Date date=new Date();
             String time = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss").format(date);
             //string中有空格，cookie不支持，故进行URL编码
             String encode = URLEncoder.encode(time, "utf-8");
             c.setValue(encode);
             //设置cookie存活时间
             c.setMaxAge(3600*24*30); //存储一个月
             response.addCookie(c);//会覆盖原来的值
             break;
           }
         }
       }
       if(cookies==null||cookies.length==0||flag) {
   %>
         <h1>欢迎您，首次访问！</h1> <%
         Date date=new Date();
         String time = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss").format(date);
         String encode = URLEncoder.encode(time, "utf-8");
         Cookie cookie=new Cookie("lastTime",encode);
         cookie.setMaxAge(3600*24*30);
         response.addCookie(cookie);
       }
     %>
   
     </body>
   </html>
   
   ```

## 指令

* 作用：用于配置JSP页面，导入资源文件

* 格式：`<%@ 指令名称 属性名1=属性值1  属性名2=属性值2 ...%>`

* 分类

  * page：配置jsp页面

    * contentType：等同于response.setContentType()
      * 设置响应体的mime类型和字符集
      * 设置当前jsp页面的编码（只能是高级IDE才能生效，如果使用记事本开发则需要设置pageEncoing来设置当前页面的字符集编码）
    * import：导包
    * errorPage：当前页面发生异常后，会自动跳转到指定的错误页面
    * isErrorPage：表示当前页面是否是错误也弥漫
      * true：可以使用内置对象exception
      * false：默认值。不可以使用内置对象exception

  * include：**用不到**。导入页面的资源文件

  * taglib：导入资源（一般用于导入标签库）

    * ```java
      <%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
      ```

    * prefix：自定义的前缀

## 注释

1. html注释`<!-- -->`只能注释html代码片段
2. （**推荐使用**）jsp注释：`<%-- --%>`可以注释所有

## 内置对象

* 在jsp页面中不需要创建，直接使用的对象

* 一共有9个               

  * 四个域对象        
  * pageContext
    * 范围：当前页面共享数据
    * 可以获取其他8个对象
  * request
    * 一次请求内共享数据
  * session
    * 一次会话的多个请求间共享数据
  * application
    * 所有用户间共享数据

  ***

  * response
    * 响应对象
  * page
    * 当前页面（Servlet）的对象 this
  * out
    * 输出对象，可以把数据输出到页面上
  * config
    * Servlet的配置对象
  * exception
    * 异常对象

# MVC：开发模式

* jsp演变历史

  1. 早期只有Servlet，只能使用response输出标签数据，非常麻烦
  2. 后来有jsp，简化了Servlet的开发，如果过度使用jsp，在jsp中既写大量的java代码，又写html标签，造成难以维护难以分工
  3. 再后来，java的web开发借鉴了mvc开发模式使得程序的设计更加合理

* MVC：

  * Model：模型
    * 完成具体的业务操作，如：查询数据库，封装对象
    * **使用JavaBean来充当**
  * View：视图
    * 展示数据
    * **使用JSP来充当**
  * Controller：控制器
    * 获取用户的输入
    * 调用模型
    * 将模型返回的数据，交给视图进行展示
    * **使用Servlet来充当**
  * 优点
    * 耦合性低，方便维护，利于分工协作
    * 重用性高
  * 缺点
    * 使得项目架构变得复杂，对开发人员要求高
    * 不适合小型项目

  [外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-bkaXsHDs-1589287359265)(会话技术cookie&session&JSP.assets/image-20200506140632847.png)]

# EL表达式

1. 概念：Expression Language 表达式语言

2. 作用：替换和简化jsp页面中java代码的编写

3. 语法：`${表达式}`

4. 注意：

   * jsp默认支持el表达式
     * `<%@ page isELIgnored="true"%>` ：设置页面忽略EL表达式
     * 加转义符`\`反斜杠：忽略当前这个EL表达式

5. 作用

   1. 运算

      * 运算符：
        1. 算数运算符：+,-,*,/(div),%(mod)
        2. 比较运算符 
        3. 逻辑运算符：&&(and)  ||(or)  !(not)
        4. 空运算符：empty
           * 功能：用于判断字符串，集合，数组对象是否为null并且长度是否为0
           * `${empty list}`  list为空或者长度为0，则该表达式返回true
           * `${not empty xxx}`：判断字符串、集合、数组对象是否不为null且长度>0

   2. 获取值

      1. **el表达式只能从域对象中获取值**

      2. 语法

         1. `${域名称.键名}`：从指定域中获取指定键的值

            * 域名称：
              1. pageScope   ——>  pageContext
              2. requestScope  ——> request
              3. sessionScope  ——> session
              4. applicationScope  ——> application (ServletContext)
            * 举例：在request域中存储了name=张三

         2. `${键名}`：表示**依次从最小的域**中查找是否有该键对应的值，直到找到为止

         3. 获取对象，List集合，Map集合的值

            1. 对象：${域名称.键名.属性名}

               * **本质上会去调用对象的get方法**

               ```java
               public class User {
                   private String name;
                   private int age;
                   private Date date;
                   
                   //逻辑视图
                   public String getBirthday(){
                       if(date==null)
                           return "";
                       return new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(date);
                   }
               ```

               ```jsp
               <%
                   User user=new User();
                   user.setName("余丰旭");
                   user.setAge(22);
                   Date mydate = new SimpleDateFormat("yyyy-MM-dd").parse("1997-12-29");
                   user.setDate(mydate);
                   request.setAttribute("user1",user);
               %>
               ${user1.name}<br>
               ${user1.age}<br>
               ${user1.birthday}<br> 	//直接按照getBirthday()方法的返回值打印
               ```

            2. List集合：

               * `${域名称.键名}`  域名可省略（**依次从最小的域**中查找是否有该键对应的值，直到找到为止）
               * ``${域名称.键名[索引]}`   如果索引越界，则什么都不显示

            3. Map集合

               * `${域名称.键名.key名称}`  域名可省略
               * `${域名称.键名["key名称"]}`

               ```java
               <%
               Map map=new HashMap();
               map.put("name","丰旭");
               map.put("user",user);
               request.setAttribute("map",map);
               %>
               ${map.name}<br>
               ${map.user.name}<br>
               ${map.user.birthday}<br>
               ```

               

      3. 隐式对象

         * 作用：例如，如果想在jsp页面的html中的一个字符串中表示post或get方式提交过来的内容，则必须使用request.getParameter()方法，但是字符串中无法调用，只能用EL表达式先通过pageContext获取requst对象
         
         * el表达式中有11个隐式对象
         * pageContext:
           * 获取jsp其他8个对象
           * 如动态获取虚拟目录：`${pageContext.request.contextPath}` 用于路径的编写

# JSTL标签

1. 概念：JavaServer Pages Tag Libray   JSP标准标签库

   * 是由apache组织提供的开源的免费的jsp标签

2. 作用：用于简化和替换jsp页面上的java代码

3. 使用步骤

   1. 导入jar包	

   2. jsp页面引入标签库：`<%taglib prefix= url=%>`

   3. 使用标签

      1. if	相当于java的if语句

         1. 属性：
            * test 必须属性，接收boolean表达式
              * 如果表达式为true，则显示if标签体内容，如果为false，则不显示
              * 一般情况下，test属性会综合el表达式一起使用
         2. 注意：c:if标签没有else情况，需要再定义if标签

         ```jsp
         <h1>判断数字是否为偶数</h1>
         <c:if test="${num%2==0}">
             ${num}是偶数
         </c:if>
         ```

         

      2. choose  相当于java的switch语句

         ```jsp
         <%
             request.setAttribute("num",8);
         %>
         <c:choose >
             <c:when test="${num==1}">星期一</c:when>
             <c:when test="${num==2}">星期二</c:when>
             <c:when test="${num==3}">星期三</c:when>
             <c:when test="${num==4}">星期四</c:when>
             <c:when test="${num==5}">星期五</c:when>
             <c:when test="${num==6}">星期六</c:when>
             <c:when test="${num==7}">星期日</c:when>
             <c:otherwise>输入数字有误</c:otherwise>
         </c:choose>
         ```

         

      3. foreach  相当于java的for语句

         1. 完成重复的操作

            * 属性

              * begin：开始值
              * end：结束值（包含）
              * var：临时变量
              * step：步长
              * varStatus：循环状态

              ```jsp
              <c:forEach begin="0" end="10" var="i" step="1" varStatus="status">
                  i的值：${i}  循环序号${status.index}  循环的总数${status.count}   <br>
              </c:forEach>
              ```

              

         2. 遍历容器

            * 属性

              * items：容器对象
              * var：容器中元素的临时变量

              ```jsp
              <%
                  List list=new ArrayList();
                  list.add(1);
                  list.add("bb");
                  list.add("ccc");
                  request.setAttribute("list",list);
              
              %>
              
              <c:forEach items="${list}" var="str" varStatus="s">
                  ${s.index}  ${s.count}   ${str}     <br>
              </c:forEach>
              ```

4. 练习

   * 需求：再request域中有一个存有User对象的List集合。需要使用jstl+el将list集合数据展示到jsp页面的表格table中。**并实现table中奇数行和偶数行颜色不同**

   * ```jsp
     <%
         List list=new ArrayList();
         User user1=new User();
         User user2=new User();
         User user3=new User();
         //User user3=new User();
         user1.setName("余丰旭");
         user2.setName("陈香玉");
         user3.setName("小小余");
         user1.setAge(22);
         user2.setAge(24);
         user3.setAge(0);
         user1.setDate(new SimpleDateFormat("yyyy-MM-dd").parse("1997-12-29"));
         user2.setDate(new SimpleDateFormat("yyyy-MM-dd").parse("1995-11-25"));
         user3.setDate(new SimpleDateFormat("yyyy-MM-dd").parse("2026-1-1"));
         list.add(user1);
         list.add(user2);
         list.add(user3);
         request.setAttribute("list",list);
     %>
     <table class="t1">
         <tr>
             <th>姓名</th>
             <th>年龄</th>
             <th>生日</th>
         </tr>
         <c:if test="${not empty list}">
         <c:forEach items="${list}" var="u" varStatus="status">
             <c:if test="${status.count%2==0}">
             <tr bgcolor="yellow">
                 <td>${u.name}</td>
                 <td>${u.age}</td>
                 <td>${u.birthday}</td>
             </tr>
             </c:if>
             <c:if test="${status.count%2!=0}">
                 <tr bgcolor="#ffc0cb">
                     <td>${u.name}</td>
                     <td>${u.age}</td>
                     <td>${u.birthday}</td>
                 </tr>
             </c:if>
         </c:forEach>
         </c:if>
     </table>
     ```

# 三层架构：软件设计架构

1. 界面层（表示层）：用户看到的界面，用户可以通过界面上的组件和服务器进行交互
2. 业务逻辑层：处理业务逻辑（如登录，注册）
3. 数据访问层：操作数据存储文件（如数据库、xml中）

[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-gsBaNMGW-1589287359267)(会话技术cookie&session&JSP.assets/image-20200507120117672.png)]

# 案例：用户信息列表展示

1. 需求：用户信息的增删改查
2. 设计：
   1. 技术选型：Servlet+JSP+MySQL+JDBCTemplate+Druid+BeanUtils+tomcat
   2. 数据库设计：
   3. 开发
      1. 环境搭建
         1. 创建数据库环境
         2. 创建项目，导入需要的jar包
      2. 编码
   4. 测试
   5. 部署运维

## 综合练习

1. 简单功能
   1. 列表查询
   2. 登录
   3. 添加
   4. 删除
   5. 修改
2. 复杂功能
   1. 删除选中
   2. 分页查询
   3. 复杂的条件查询，带有分页效果

## 登录功能

1. 调整页面，加入验证码功能