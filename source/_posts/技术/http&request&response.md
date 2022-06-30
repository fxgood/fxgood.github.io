---
title: http&request&response
date: 2020-05-12
category: 技术
tag:
- web
excerpt: 记录http、request和response的基础知识和相关应用案例。
---

# HTTP

* 概念：Hyber Text Transfer Protocol 超文本传输协议

  * 传输协议：定义了客户端与服务端通信时，发送数据的格式
  * 特点：
    * 基于TCP/IP的高级协议
    * 默认端口80
    * 基于请求/响应模型：一次请求对应一次响应
    * 无状态的：**每次请求之间相互独立**，不能交互数据
  * 历史版本：
    * 1.0：每次请求都会建立新的连接（**非持久连接**）
    * 1.1 ：复用连接（**持久连接**）

* 请求消息数据格式

  1. 请求行

     * 请求方式 请求url路径 请求的协议/版本

       GET /login.html  HTTP/1.1

     * 请求方式：

       * HTTP协议有其7种请求方式，常用两种
         * GET:
           1. 请求参数在请求行中（在url后）
              `Request URL: http://localhost:8080/demo03?username=12`
           2. 请求url长度有限制
           3. 不安全
         * POST:
           * 请求参数在请求体中
           * 请求url长度无限制
           * 相对安全

  2. 请求头：客户端浏览器告诉服务器一些信息

     * 请求头名称：请求头值 （同样的格式很多行）
     * 常见的请求头：
       1. User-Agent：浏览器告诉服务器，我访问你使用的浏览器版本
          * 可以在服务器端获取该头的信息，解决浏览器的兼容性问题
       2. Referer：http://localhost/table.html
          * 告诉服务器“我”从哪里来
          * 作用：
            * 放盗链
            * 统计工作

  3. 请求空行

     * 空行，分割作用

  4. 请求体（正文）

     * 封装POST请求消息的请求参数的

* 响应消息：服务器端发送给客户端的数据

  * 数据格式

    * 响应行

      * 组成：协议/版本 响应状态码 状态码的描述

      * 响应状态码：服务器告诉客户端浏览器本次请求和响应的状态

        * 状态码都是3位数字

        * 分类：五大类（具体的去搜百度 http状态码）

          * 1xx：服务器收到客户端消息，但没有接收完成，等待一段时间后，发送1xx多状态码（问客户端，你还有没有消息要发给我）

          * 2xx：成功。代表：200

          * 3xx：重定向。代表：302（重定向），304（服务器上该内容没有更改，去访问缓存）

            [外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-qSkGOoVl-1589287182468)(http&request&response.assets/image-20200504212311002.png)]

            [外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-EbZO16bU-1589287182472)(http&request&response.assets/image-20200504212351845.png)]

          * 4xx：客户端错误：

            * 404（请求路径没有对应的资源）
            * 405：请求方式没有对应的doXXX（doGet/doPost..)方法

          * 5xx：服务器端错误.  代表：500（服务器内部出现异常）

    * 响应头

      1. 格式：（每行都是）头名称：值
      2. 常见的响应头
         1. Content-Type：服务器告诉客户端浏览器本次响应消息体数据格式以及编码格式
         2. Content-dispostion：服务器告诉客户端以什么格式打开响应体数据
            1. 值：
               * 默认值：in-line“默认值，在当前页面内打开
               * attachment；filename=xxx：以附件形式打开响应体。文件下载常用

    * 响应空行

    * 响应体：传输的数据

  * 响应字符串格式

# Request

* web服务的原理

1. tomcat服务器会根据url中的资源路径，创建对应的ServletDemo01对象
2. tomcat服务器会创建request对象和response对象，request对象中封装请求消息数据
3. tomcat将request和response两个对象传递给service方法，并且调用service方法
4. 程序员，可以通过request对象获取请求消息数据，通过response对象设置响应消息数据
5. 服务器在给浏览器做出响应之前，会从response对象中拿程序员设置的响应消息数据

* request对象和response对象原理

  1. request对象和response对象是由服务器创建的，我们来使用
  2. request对象用来获取请求消息，response对象用来设置响应消息

* request对象继承体系结构

  ServletRequest ——接口

  |

  HttpServletRequest ——接口

  |

  org.apache.catalina.connector.RequestFacade 类（tomcat创建的）

* request功能：

  1. 获取请求消息数据

     1. 获取请求行数据

        * `get /day14/demo01?name=zhangsan HTTP/1.1`
        * 方法
          1. 获取请求方法
             String getMethod()
          2. (**用的多**)获取虚拟目录：/day14
             String getContextPath()
          3. 获取Servlet路径：/demo1
             String getServletPath()
          4. 获取get方式的请求参数:name=zhangsan
             String getQueryString() 
          5. (**用的多**)获取请求URI:  /day14/demo1
             String getRequestURI()
             String getRequestURL()：`http://localhost/day14/demo1`
             * URL：统一资源定位符：中华人民共和国
             * URI：统一资源标识符：共和国
          6. 获取协议及版本：HTTP/1.1
             String getProtocol()
          7. 获取客户机的IP地址
             String getRemoteAddr()

     2. 获取请求头数据

        * 方法

          1. String getHeader(String name)：通过请求头的名称获取请求头的值

          2. (**用的少**)Enumberation\<String> getHeaderNames()：获取所有请求头的名称 (Enumberation，当成迭代器来使用)

             ```java
             //获取所有请求头名称
                     Enumeration<String> headerNames = request.getHeaderNames();
                     while(headerNames.hasMoreElements()){
                         //根据请求头名称获取请求头的值
                         String name = headerNames.nextElement();
                         String value=request.getHeader(name);
                         System.out.println(name+"--"+value);
                     }
             ```

             

     3. 获取请求体数据

        * 请求体：只有POST请求方式，才有请求体，在请求体中封装了POST请求的请求参数

        * 步骤：

          1. 获取流对象

             * BufferedReader getReader()：获取字符输入流
             * ServletInputStream getInputStream()：获取字节输入流，可以操作所有类型数据（文件上传知识点后讲解）

          2. 从流对象中拿数据

             ```java
             //获取请求体流对象
                     BufferedReader reader = request.getReader();
                     //读取数据
                     String line=null;
                     while((line=reader.readLine())!=null){
                         System.out.println(line);
                     }
             ```

  2. 其他功能

     1. 获取请求参数通用方式(不论是post还是get都可以用一下方式获取参数)

        ```java
        protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                this.doPost(request,response);
            }
        ```

        

        * getParameter(String name)：根据参数名称来获取参数值 
        * getParameterValues(String name)：根据参数名称获取参数值的数组  hobby=learn&hobby=game
        * Enumeration getParameterNames()：获取所有请求的参数名称
        * Map<String,String[]>getParameterMap()：获取所有参数的map集合
        * **中文乱码问题**
          * get方式：tomcat8已经帮我们解决
          * post方式：会乱码
            * 解决：在获取参数前，设置request的编码`request.setCharacterEncoding("UTF-8");`

     2. 请求转发：一种在服务器内部的资源跳转方式（ 多个Servlet配合实现一种功能）

        * 步骤
              1. 通过request对象获取请求转发器对象
              2. 使用RequestDispatcher对象进行转发(其参数是要转向的servlet的地址)
                 * forward（request,response）
            * 特点
              1. 浏览器地址栏路径没有发生变化
              2. 只能转发到当前服务器内部资源中，无法转发到外部网站
              3. 转发是一次请求

     3. 共享数据

        * 域对象：一个有作用范围的对象，可以在范围内共享数据
        * request域：代表一次请求的范围，一般用于请求转发的多个资源中共享数据
        * 方法
          1. setAttribute(String name，Object obj)：存储数据
          2. getAttribute(String name)：通过键获取值，返回的是obj对象
          3. removeAttribute(String name)：通过键移除键值对

     4. 获取ServletContext对象

        * request.getServletContext()

# ⭐登录案例

## 需求

1. 编写login.html登录页面

   username&password两个输入框

2. 使用Druid数据库连接池，操作mysql数据库

3. 使用jdbcTemplate技术封装JDBC

4. 登录成功跳转到SuccessServlet展示：登录成功！用户名，欢迎您

5. 登录失败跳转到FailServlet展示：登录失败，用户名或密码错误

## 登录逻辑

[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-jbPzoW9m-1589287182473)(http&request&response.assets/image-20200502204747437.png)]

## 

***

## 具体案例		

1. 创建Java EE工程，选择Web Application 取消下面的Create web.xml勾选

2. 在web文件夹下创建WEB-INF，再下面创建lib，将依赖jar包全部放入（mysql驱动，spring的jdbcTemplate，druid连接池）**r然后右键lib点击add as library**

3. 将druid.properties放在src下（记得修改url）

   ```
   driverClassName=com.mysql.jdbc.Driver
   url=jdbc:mysql:///db1
   username=root
   password=root
   initialSize=5
   maxActive=10
   maxWait=3000
   ```

   

4. 创建数据库环境（创建表，添加数据）

5. 创建包com.hhu.domain,创建User类JavaBean

   ```java
   /*User的实体类，JavaBean*/
   public class User {
       private int id;
       private String username;
       private String password;
   
       @Override
       public String toString() {
           return "User{" +
                   "id=" + id +
                   ", username='" + username + '\'' +
                   ", password='" + password + '\'' +
                   '}';
       }
   
       public int getId() {
           return id;
       }
   
       public void setId(int id) {
           this.id = id;
       }
   
       public String getUsername() {
           return username;
       }
   
       public void setUsername(String username) {
           this.username = username;
       }
   
       public String getPassword() {
           return password;
       }
   
       public void setPassword(String password) {
           this.password = password;
       }
   }
   ```

   

6. 创建com.hhu.util.JDBCUtils类，使用Druid连接池

   ```java
   public class JDBCUtils {
       //静态成员变量：数据库连接池
       private static DataSource dataSource;
       //加载配置文件
       static{
           Properties properties=new Properties();
           InputStream resourceAsStream = JDBCUtils.class.getClassLoader().getResourceAsStream("druid.properties");
           try {
               //将配置文件加载到集合中
               properties.load(resourceAsStream);
               //从集合中读取配置，生成数据库连接池
               dataSource= DruidDataSourceFactory.createDataSource(properties);
           } catch (IOException e) {
               e.printStackTrace();
           } catch (Exception e) {
               e.printStackTrace();
           }
       }
       //获取数据库连接池
       public static DataSource getDataSource(){
           return dataSource;
       }
   
       public static Connection getConnection() throws SQLException {
           return dataSource.getConnection();
       }
   }
   
   ```

   

7. 创建包com.hhu.dao,创建类UserDao，提供Login方法

   ```java
   public class UserDao {
       //声明jdbcTemplate对象，来公用（因为以后很多方法要用到）
       private JdbcTemplate jdbcTemplate=new JdbcTemplate(JDBCUtils.getDataSource());
       public User login(User loginUser){
           try {
               //1.编写sql
               String sql="select* from user where username=? and password=?";
               //2.调用query方法
               User user = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<>(User.class),
                       loginUser.getUsername(),
                       loginUser.getPassword());
               return user;
           } catch (DataAccessException e) {
               //e.printStackTrace();
               return null;
           }
       }
   }
   
   ```

   

8. 编写com.hhu.web.servlet.LoginServlet类(和成功/失败Servlet），完成登录逻辑

   ```java
   @WebServlet("/loginServlet")
   public class loginServlet extends HttpServlet {
       protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
           //设置编码
           request.setCharacterEncoding("UTF-8");
           //接收请求参数
           String username = request.getParameter("username");
           String password = request.getParameter("password");
           //封装User对象
           User loginUser=new User();
           loginUser.setUsername(username);
           loginUser.setPassword(password);
           //调用DAO实现登录功能
           User user = new UserDao().login(loginUser);
           if(user==null){
               request.getRequestDispatcher("/failServlet").forward(request,response);
           }
           else {
               //存储
               request.setAttribute("user",user);
               //转发
               request.getRequestDispatcher("/successServlet").forward(request, response);
           }
       }
   
       protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
           this.doPost(request,response);
       }
   }
   
   ```

   **failServlet**

   ```java
   @WebServlet("/failServlet")
   public class failServlet extends HttpServlet {
       protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
           //设置编码
           response.setContentType("text/html;charset=utf-8");
           //输出
           response.getWriter().write("登录失败，用户名或密码错误");
       }
   
       protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
           this.doPost(request,response);
       }
   }
   
   ```

   ```successServlet
   @WebServlet("/successServlet")
   public class successServlet extends HttpServlet {
       protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
           //设置编码
           response.setContentType("text/html;charset=utf-8");
   
           //输出
           User user=(User)request.getAttribute("user");
   
           response.getWriter().write("登录成功！"+user.getUsername()+"，欢迎您！");
       }
   
       protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
           this.doPost(request,response);
       }
   }
   
   ```

   

   1. 最外面那个web目录下创建login.html，**注意，login.html中form表单的action路径的写法：虚拟目录+servlet的urlpattern**

      ```java
      <!DOCTYPE html>
      <html lang="en">
      <head>
          <meta charset="UTF-8">
          <title>登录</title>
      </head>
      <body>
      <form action="/web/loginServlet" method="post">
          <input name="username" type="text" placeholder="输入用户名">
          <input name="password" type="text" placeholder="输入密码">
          <input type="submit" value="登录">
      </form>
      </body>
      </html>
      ```

9. BeanUtils工具类，**简化数据封装**

   * 用于封装JavaBean
     * JavaBean：标准的java类
       * 类必须被public修饰
       * 必须提供空参的构造器
       * 成员变量必须用private修饰
       * public修饰的setter和getter
     * 功能：封装数据
     * 方法：
       * setProperty（）
       * getProperty（）
       * populate（Object obj,Map map）：将map集合的键值对信息，封装到对应的JavaBean对象中
   * 概念：
     * 成员变量和属性：属性大多数时候跟成员变量一样，但是实质是setter和getter截取后的产物，特例情况下和成员变量不同
     * 如getUsername()  属性就是username

   **注意是导入apache下的BeanUtils**

   ```java
   /* //接收请求参数
           String username = request.getParameter("username");
           String password = request.getParameter("password");
           //封装User对象
           User loginUser=new User();
           loginUser.setUsername(username);
           loginUser.setPassword(password);*/
   将上面的注释掉，改为下面的
           //获取所有请求参数，封装成一个对象
           Map<String, String[]> parameterMap = request.getParameterMap();
           User loginUser=new User();
           //注意是导入apache下的BeanUtils
           try {
               BeanUtils.populate(loginUser,parameterMap);
           } catch (IllegalAccessException e) {
               e.printStackTrace();
           } catch (InvocationTargetException e) {
               e.printStackTrace();
           }
   ```

   

# Response

* 功能：设置响应消息

  1. 设置相应行
     1. 格式：HTTP/1.1 200 ok
     2. 状态码设置：setStatus(int sc)
  2. 设置响应头
     * setHeader(String name,String value)
  3. 设置响应体
     * 使用步骤：
       1. 获取输出流
          1. 字符输出流：printWriter getWriter()
          2. 字节输出流:ServletOutputStream getOutputStream()
       2. 使用输出流，将数据输出到客户端浏览器

* 案例：

  1. 完成重定向

     * 重定向：资源跳转的方式

     * 与前面学的Servlet的转发不同之处在于：Servlet的转发是服务器内部的资源跳转，而重定向是服务器返回消息告诉浏览器去别的Servlet去找资源

     * 代码实现

       ```java
               //1.设置状态码为302
               response.setStatus(302);
               //2.设置响应头location
               response.setHeader("location","/web/responseDemo2");
               //更为简单的实现重定向的方法
               response.sendRedirect("/web/responseDemo2");
       ```

     * 重定向的特点(redirect)

       * 地址栏发生变化
       * 重定向可以访问其他站点（服务器）的资源
       * 重定向是两次请求

     * 转发的特点（forward）

       * 转发地址栏路径不变
       * 转发只能访问当前服务器下的资源
       * 转发是**一次请求**（即可以用request.setAttribute()来完成一次请求内部的数据共享）

     * 路径写法：

       1. 分类
          1. 相对路径：通过相对路径不可以确定唯一资源
             * 如：./index.html
             * 不以`/`开头，以`./`开头
             * 规则：找到当前资源和目标资源之间的相对位置关系
               * `./`：当前目录（可以省略）
               * `../`：后退一级目录
          2. （**推荐使用**）绝对路径：通过绝对路径可以确定唯一资源
             * 如：http://localhost/day15/responseDemo02
             * 可以简写成`/day15/responseDemo02`
             * 以`/`开头的路径
             * 规则：判断定义的路径是给谁用的（从谁发出的）
               * 给浏览器使用：需要加虚拟目录（项目的访问路径：）（如重定向，超链接，表单action）
                 * **建议虚拟目录动态获取：使用request.getContextPath()获取后拼接到绝对路径前面**
               * 给服务器用：不需要加虚拟目录（如Servlet的转发）

  2. 服务器输出字符数据到浏览器

     * 步骤

       1. 获取字符输出流
       2. 输出数据
          * 中文乱码原因：
            * 编码节码使用的字符集不同
            * PrintWriter pw=response.getWriter()获取的流的默认编码是ISO-8895-1
            * 浏览器字符集：默认GBK（gb2312）
          * 解决办法：获取流之前设置`response.setContentType("text/html;charset=utf-8");`

     * 代码

       ```java
               response.setContentType("text/html;charset=utf-8");
               //1.获取字符输出流
               PrintWriter pw = response.getWriter();
               //2.输出数据
               pw.write("<h1>helloworld</h1>");
               pw.write("<h1>你好你好你好</h1>");
       ```

       

  3. 服务器输出字节数据到浏览器

     * 步骤

       1. 获取字节输出流
       2. 输出数据

     * 代码

       ```java
        //1.获取字节输出流
               ServletOutputStream outputStream = response.getOutputStream();
               //2.输出数据
               outputStream.write("helloworld".getBytes());
               outputStream.write("你好".getBytes());
       ```

       

  4. 验证码案例

     * 本质：图片
     * 目的：防止恶意表单注册
     * 略，毕设暂不需要

# ServletContext

1. 概念：代表整个web应用，可以和程序的容器（服务器）来通信

2. 获取：

   1. 通过request对象获取：request.getServletContext()
   2. 通过HttpServlet获取：this.getServletContext()
   3. 两种方式获取的ServletContext是同一个（都代表本web应用）

3. 功能：
   1. 获取MIME类型：

      * MIME类型：在互联网通信过程中定义的一种文件数类型

        * 格式：大类型/小类型  如text/html、image/jpeg

        * 获取：String getMimeType(String file)

          ```java
          ServletContext servletContext = request.getServletContext();
                  String filename="a.jpg";
                  String mimeType = servletContext.getMimeType(filename);
                  response.getWriter().write(mimeType); 
          ```

          

   2. 域对象：共享数据

      1. setAttribute(String name,object value):
      2. getAttribute(String name)
      3. removeAttribut4e(String name)

      * ServletContext对象范围：所有用户请求的数据（只要服务器开着，其中的域共享所有数据）
        * 生命周期过长，谨慎使用，容易占用太多内存

   3. 获取文件的真实（服务器）路径

      * web项目的文件有两份，一份在本地的工作空间，另一份在tomcat服务器中，别人访问的都是tomcat中的内容
      * 方法：
        * (**重要**)getRealPath(String path)
          * getRealPath("b.txt")：web目录下资源访问
          * getRealPath("/WEB-INF/c.txt")：WEB-INF目录下的资源访问
          * getRealPath("/WEB=INF/classes/a.txt")  src路径下的资源访问

# 文件下载案例

需求：

1. 页面显示超链接
2. 点击超链接之后弹出下载提示框
3. 完成图片文件西在

分析：

1. 超链接指向的资源如果能够被浏览器解析，则在浏览器中展示，如果不能，则弹出下载
2. **我们的需求：任何资源必须弹出下载提示框**
3. 使用响应头设置资源的打开方式：
   1. response.setHeader()
   2. content-dispostion: attachment;filename=xxx

步骤：

1. 定义页面，编辑超链接href属性，指向一个Servlet，传递资源名称filename
2. 定义Servlet
   1. 获取文件的名称
   2. 使用字节输入流加载文件进内存（ServletContext获取文件真实路径）
   3. 指定response的响应头 content-dispostion: attachment;filename=xxx
   4. 将数据写出到response输出流

代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<a href="/project/downloadServlet?filename=bg4.png">图片</a>
</body>
</html>
```

```java
@WebServlet("/downloadServlet")
public class downloadServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //获取请求参数：文件名称
        String filename = request.getParameter("filename");
        //找到文件真实路径
        String realPath = this.getServletContext().getRealPath("/img/" + filename);
        //加载文件进内存，通过字节流关联
        FileInputStream fileInputStream=new FileInputStream(realPath);

        //设置response响应头
        //设置响应头类型
        ServletContext servletContext = this.getServletContext();//获取文件的mime类型
        response.setHeader("content-type",servletContext.getMimeType(filename));
        //设置响应头打开方式
        response.setHeader("content-disposition","attachment;filename="+filename);

        //将输入流的数据，写到输出流中
        ServletOutputStream outputStream = response.getOutputStream();
        byte[]buff=new byte[1024*8];
        int len=0;
        while((len=fileInputStream.read(buff))!=-1){    //不等于-1，说明没有到文件末尾
            outputStream.write(buff,0,len);
        }
        fileInputStream.close(); //输入流要关，输出流不用
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request, response);
    }
}
```



## 中文文件名问题

解决思路：

1. 获取客户端使用的浏览器版本信息
2. 根据不同的版本信息，设置filename的编码方法不同

工具类：

```java
package cn.itcast.utils;

import sun.misc.BASE64Encoder;
import java.io.UnsupportedEncodingException;
import java.net.URLEncoder;


public class DownLoadUtils {

    public static String getFileName(String agent, String filename) throws UnsupportedEncodingException {
        if (agent.contains("MSIE")) {
            // IE浏览器
            filename = URLEncoder.encode(filename, "utf-8");
            filename = filename.replace("+", " ");
        } else if (agent.contains("Firefox")) {
            // 火狐浏览器
            BASE64Encoder base64Encoder = new BASE64Encoder();
            filename = "=?utf-8?B?" + base64Encoder.encode(filename.getBytes("utf-8")) + "?=";
        } else {
            // 其它浏览器
            filename = URLEncoder.encode(filename, "utf-8");
        }
        return filename;
    }
}

```

```java
//获取user-agent请求头
String agent=request.getHead("user-agent");
String filename=DownLoadUtils.getFileName(agent,filename);
//使用这个filename即可
```