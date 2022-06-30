---
date: 2020-05-12
title: JDBC学习笔记
category: 技术
tag: 中间件
excerpt: 记录学习jdbc的过程中的一些要点。
---

# JDBC基本概念

* Java DataBase Connectivity   **Java数据库连接，Java语言操作数据库**
* JDBC本质：是Sun公司定义的一套操作关系型数据库的接口，各个数据库厂商去实现了这套接口，提供数据库驱动jar包。我们可以使用这套接口编程

# 快速入门

* 步骤

  1. 导入驱动jar包

     1. 复制文件到libs目录下(记得把数据库驱动也复制过去)
     2. 右键add as library

  2. 注册驱动

  3. 获取数据库的连接对象Connection（java与数据库的连接桥梁）

  4. 定义sql

  5. 获取执行sql语句的对象Statement

  6. 执行sql，接收返回结果

  7. 处理结果

  8. 释放资源

  9. **如果自定义JDBCUtils，则须在src下创建jdbc.properties**

     ```html
     url=jdbc:mysql:///db1
     user=root
     password=root
     driver=com.mysql.jdbc.Driver
     ```

     

* 代码实现

  ```java
  //1.导入jar包
          //2.注册驱动
          Class.forName("com.mysql.jdbc.Driver");
          //3.获取数据库连接对象
          Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/db1", "root", "123");
          //4.定义sql语句
          String sql ="update user set name='goodboy' where id=1";
          //5.获取执行sql的对象
          Statement statement = connection.createStatement();
          //6.执行sql
          int count = statement.executeUpdate(sql);
          //7.处理结果
          System.out.println(count);
          //8.释放资源
          statement.close();
          connection.close();
  ```

  

* 详解各个对象

  * DriverManager：驱动管理对象

    * 功能：
      1. 注册驱动：告诉程序该使用哪个数据库驱动jar（**！mysql5以后自动注册**）
         * static void registerDriver(Driver driver)：注册与给定的驱动程序DriverManager
         * 写代码使用：Class.forName("com.mysql.jdbc.Driver");
         * 通过查看源码发现：在com.mysql.jdbc.Driver类中存在存在静态代码块
      2. 获取数据库连接 ：
         * 方法：getConnection(String url,String user,String password)
           * 参数
             * url:指定连接的路径
               * mysql语法：jdbc:mysql://ip地址（域名）:端口号/数据库名称
               * 例子：jdbc:mysql:..localhost(127.0.0.1):3306/bd1
               * 细节：如果连接的是本机的mysql服务器，并且mysql服务默认端口是3306，则url可以简写为jdbc:mysql:///数据库名称
             * user：数据库用户名
             * password：数据库密码

  * Connection：数据库连接对象

    * 功能
      1. 获取执行sql的对象
         * createStatement()
         * prepareStatement(String sql)
      2. 管理事务
         1. 开启事务： setAutoCommit(boolean autoCommit)：调用该方法设置参数为false，即开启事务
         2. 提交事务：commit()
         3. 回滚事务：rollback()

  * Statement：执行sql的对象

    1. 执行sql
       1. boolean execute(String sql)：可以执行任意sql，**了解即可不常用**
       2. int excuteUpdate(String sql)：**执行DML（增删改insert，update，delete）语句**，（DDL不常用）DDL（create,alter,drop)
          * 返回值：影响的行数。>0执行成功
       3. （**最常用**）ResultSet excuteQuery(String sql)：执行DQL（Select）语句

  * ResultSet：结果集对象,封装查询结果

    * boolean next()：游标向下移动衣阿华那个
    * getXxx(参数)：获取数据
      * Xxx：代表数据类型 如getInt() getString()
      * 参数：
        * int：代表列的编号，**从1开始**
        * String：代表列名称。如getString("name");
      * 使用步骤
        * 游标向下移动一行
        * 判断是否有数据(看next的返回结果，如果为false则该行没数据  )
        * 获取数据

  * 练习

    * 定义一个方法，查询emp表的数据将其封装为对象，然后装载集合，返回（注意double类型接收数据库的小数）

      ```java
      /*
      * 查询emp表然后将结果封装成一个对象，然后放入集合，返回*/
      public class JDBC_demo05 {
          /*查询emp所有对象*/
          public static List<User> findAll(){
              Connection connection=null;
              Statement statement=null;
              ResultSet resultSet=null;
              ArrayList<User>list=new ArrayList<>();
              try {
                  //1.注册驱动
                  Class.forName("com.mysql.jdbc.Driver");
                  //2.获取连接
                  connection= DriverManager.getConnection("jdbc:mysql:///db1","root","");
                  //3.创建sql
                  String sql="select*from user";
                  //4.执行sql
                  statement=connection.createStatement();
                  resultSet = statement.executeQuery(sql);
                  //5.处理结果
                  while(resultSet.next()){
                      User user=new User();
                      user.setId(resultSet.getInt("id"));
                      user.setName(resultSet.getString("name"));
                      user.setGender(resultSet.getString("gender"));
                      list.add(user);
                  }
              } catch (ClassNotFoundException | SQLException e) {
                  e.printStackTrace();
              }finally {
      
                  try {
                      if(resultSet!=null)
                          resultSet.close();
                  } catch (SQLException throwables) {
                      throwables.printStackTrace();
                  }
                  try {
                      if(statement!=null)
                              statement.close();
                  } catch (SQLException throwables) {
                      throwables.printStackTrace();
                  }
                  try {
                      if(connection!=null)
                              connection.close();
                  } catch (SQLException throwables) {
                      throwables.printStackTrace();
                  }
      
              }
              return list;
          }
      
          public static void main(String[] args) {
              List<User> all = JDBC_demo05.findAll();
              for(User user:all){
                  System.out.println(user.getId()+" "+user.getName()+" "+user.getGender());
              }
          }
      }
      ```

      

  * PreparedStatement：执行sql的对象（是Statement的子接口）

    1. SQL注入问题：在拼接sql时，有一些sql的特殊关键词参与字符串的拼接。会造成安全性问题
       1. 用户名随便，密码输入 a' or 'a'='a'
       2. sql: select*from user where username=xxx and password='a' **or 'a'='a'**,查询条件变成了恒等式
    2. 解决：使用PreparedStatement对象来解决
    3. 预编译的SQL：参数？作为占位符
    4. 步骤：
       1. 导入驱动jar包 
       2. 注册驱动
       3. 获取数据库连接对象COnnection
       4. 定义sql
          * 注意：sql的参数使用？作为占位符
       5. 获取PreparedStatement对象：Connection.getPreparedStatement(**String sql**)
       6. 给？赋值
          * 方法：setXxx（参数1，参数2）
            * 参数1：？的位置编号，从1开始
            * 参数2：？的值
    5. **注意**：**后期都会使用PreparedStatement来完成增删改查的所有工作**
       1. 可以防止SQL注入
       2. 效率更高

* 代码实现

  ```java
  public class JDBC_demo03_标准 {
      public static void main(String[] args) {
          Connection connection=null;
          Statement statement=null;
          //1.导驱动jar包
          try {
              //2.设置驱动
              Class.forName("com.mysql.jdbc.Driver");
              //3.获取连接数据库对象
              connection = DriverManager.getConnection("jdbc:mysql:///db1","root","");
              //4.定义sql语句
              String sql="insert into user values(3,'小小余','男')";
              //5.执行
              statement = connection.createStatement();
              int result = statement.executeUpdate(sql);
              //6.打印结果
              if(result>0)
                  System.out.println("执行成功！");
              else
                  System.out.println("执行失败！");
          } catch (ClassNotFoundException | SQLException e) {
              e.printStackTrace();
          }finally {
              try {
                  if(statement!=null)
                      statement.close();
                  if(connection!=null)
                      connection.close();
              } catch (SQLException throwables) {
                  throwables.printStackTrace();
              }
  
          }
      }
  }
  ```

  ```java
  public class JDBC_demo04_excuteQuery {
      public static void main(String[] args) {
          Connection connection=null;
          Statement statement=null;
          ResultSet resultSet=null;
  
          //1.导驱动jar包
          try {
              //2.设置驱动
              Class.forName("com.mysql.jdbc.Driver");
              //3.获取连接数据库对象
              connection = DriverManager.getConnection("jdbc:mysql:///db1","root","");
              //4.定义sql语句
              String sql="select*from user";
              //5.执行
              statement = connection.createStatement();
              resultSet=statement.executeQuery(sql);
              //6.打印结果
              while(resultSet.next()){
                  int id=resultSet.getInt("id");
                  String name=resultSet.getString("name");
                  String gender=resultSet.getString("gender");
                  System.out.println(id+"/"+name+"/"+gender);
              }
          } catch (ClassNotFoundException | SQLException e) {
              e.printStackTrace();
          }finally {
              try {
                  if(resultSet!=null)
                      resultSet.close();
                  if(statement!=null)
                      statement.close();
                  if(connection!=null)
                      connection.close();
              } catch (SQLException throwables) {
                  throwables.printStackTrace();
              }
  
          }
      }
  }
  ```

# 抽取JDBC工具类：JDBCUtils

* 目的：简化书写
* 案例：

**User**: 封装了数据库表的JavaBean

```java
public class User {
    private int id;
    private String name;
    private String gender;

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", gender='" + gender + '\'' +
                '}';
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getGender() {
        return gender;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    public User() {
    }
}
```

**JDBCUtils工具类**：

```java
public class JDBCUtils {
    private static String url;
    private static String user;
    private static String password;
    private static String driver;


    //静态代码块加载配置文件,**静态代码块随着类的加载而加载，只执行一次**
    static {
        Properties properties=new Properties();

        //获取src路径下的文件的方式——>ClassLoader类加载器
        ClassLoader classLoader = JDBCUtils.class.getClassLoader();
        URL propertiesURL = classLoader.getResource("jdbc.properties");
        try {
            properties.load(new FileReader(propertiesURL.getPath()));
        } catch (IOException e) {
            e.printStackTrace();
        }
        url=properties.getProperty("url");
        user=properties.getProperty("user");
        password=properties.getProperty("password");
        driver=properties.getProperty("driver");
        //注册驱动
        try {
            Class.forName(driver);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    //获取Connection对象
    public static Connection getConnection(){
        Connection connection=null;
        try {
            connection= DriverManager.getConnection(url, user, password);
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }
        return connection;
    }

    //执行sql语句,实现增删改
    public static void update(String sql){
        Connection connection=getConnection();
        Statement statement=null;
        int result=-1;
        try {
            statement = connection.createStatement();
            result = statement.executeUpdate(sql);
            if(result>0)
                System.out.println("执行成功！");

        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }finally {
            closeUpdate(connection,statement);
        }

    }
    //update后关闭资源
    public static void closeQuery(Connection connection, ResultSet resultSet, Statement statement){
        if(resultSet!=null) {
            try {
                resultSet.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if(statement!=null) {
            try {
                statement.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if(connection!=null) {
            try {
                connection.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }

    }
    //query后关闭资源
    public static void closeUpdate(Connection connection,Statement statement){
        if(statement!=null) {
            try {
                statement.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if(connection!=null) {
            try {
                connection.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }
}
```

**实现增删改**

```java
public class JDBC_demo06 {
    public static void main(String[] args) {
        Connection connection= JDBCUtils.getConnection();
        String sql="insert into user values(4,'小小余','女')";
        JDBCUtils.update(sql);
    }
}
```

**实现查询**

```java
public class JDBC_demo07 {
    public static void main(String[] args) {
        Statement statement=null;
        ResultSet resultSet=null;
        String sql="select*from user";
        Connection connection= JDBCUtils.getConnection();
        ArrayList<User>list=new ArrayList<>();
        try {
            statement=connection.createStatement();
            resultSet=statement.executeQuery(sql);
            while(resultSet.next()){
                User user=new User();
                user.setId(resultSet.getInt("id"));
                user.setName(resultSet.getString("name"));
                user.setGender(resultSet.getString("gender"));
                list.add(user);
            }
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }
        JDBCUtils.closeQuery(connection,resultSet,statement);
        for(User user:list){
            System.out.println(user);
        }

    }
}

```

# JDBC控制事务

1. 事务：一个包含多个步骤的业务操作 
2. 操作：
   1. 开启事务
   2. 提交事务
   3. 回滚事务
3. 使用Connection对象来管理事务
   1. setAutoCommit(boolean autoCommit（）：调用该方法，设置参数为false，即开启事务
      * 在执行sql之前开启事务
   2. commit()：提交事务
      * 当所有sql都执行完提交事务
   3. rollback()：回滚事务
      * 在catch中回滚事务

***

# 数据库连接池

* 概念：其实就是一个容器（集合）：存放数据库连接的容。

* 当系统初始化好后，容器被创建，容器中会申请一些连接对象，当用户访问数据库时，从容器中获取连接对象，用户访问完后，会将对象归还给容器

* 好处

  * 节约资源
  * 高效

* 实现：

  1. 标准接口：DataSource  javax.sql包下的

     * 方法：
       * 获取连接：getConnection()
       * 归还连接：如果连接对象COnnection是从连接池中获取的，那么调用Connection.close()方法，则不会再关闭连接，而是归还到连接池中

  2. 一般我们不去实现它，由数据库厂商来实现

     1. C3P0：数据库连接池技术（老）
     2. Druid（德鲁伊）：由阿里巴巴来实现的

  3. C3P0 略

  4. Druid：数据库连接池实现技术，由阿里巴巴提供的

     * 步骤：
       1. 导入jar包 druid-1.0.9.jar
       2. 定义配置文件
          * 是properties形式的
          * 可以叫任意名称，可以放在任意目录下
       3. 加载配置文件。Properties
       4. 获取数据库连接池对象：通过工厂类来获取DruidDataSourceFactory
       5. 获取连接：getConnection()

  5. 代码实现

     ```java
             //1.导入jar包，放入libs，右键add as library
             //2.定义配置文件，赋值，放在src下(记得修改一下url）
             //3.加载配置文件
             Properties properties=new Properties();
             InputStream inputStream = DruidDemo.class.getClassLoader().getResourceAsStream("druid.properties");
             properties.load(inputStream);
             //4.获取连接池对象
             DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
             //5.获取连接
             Connection connection=dataSource.getConnection();
             System.out.println(connection);
     ```

* 定义工具类

  1. 定义一个类JDBCUtils

  2. 提供静态代码块加载配置文件，初始化连接池对象

  3. 提供方法

     1. 获取连接方法：通过数据库连接池获取连接
     2. 释放资源：
     3. 获取连接池的方法

     ```java
     /*Druid连接池的工具类*/
     public class JDBCUtils_Druid {
         //1.定义成员变量DataSource
         private static DataSource dataSource;
         static{
             //1.加载配置文件
             Properties properties=new Properties();
             InputStream inputStream = JDBCUtils_Druid.class.getClassLoader().getResourceAsStream("druid.properties");
             try {
                 properties.load(inputStream);
                 dataSource = DruidDataSourceFactory.createDataSource(properties);
             } catch (IOException e) {
                 e.printStackTrace();
             } catch (Exception e) {
                 e.printStackTrace();
             }
         }
         //2.获取连接的方法
         public static Connection getConnection(){
             Connection connection=null;
             try {
                 connection=dataSource.getConnection();
             } catch (SQLException throwables) {
                 throwables.printStackTrace();
             }
             return connection;
         }
         //3.释放资源
         public static void close(Statement statement,Connection connection){
             if(statement!=null) {
                 try {
                     statement.close();
                 } catch (SQLException throwables) {
                     throwables.printStackTrace();
                 }
             }
             if(connection!=null) {
                 try {
                     connection.close(); //归还连接
                 } catch (SQLException throwables) {
                     throwables.printStackTrace();
                 }
             }
         }
         public static void close(ResultSet resultSet,Statement statement, Connection connection){
             if(resultSet!=null) {
                 try {
                     resultSet.close();
                 } catch (SQLException throwables) {
                     throwables.printStackTrace();
                 }
             }
             close(statement,connection);
         }
         //4.获取连接池的方法
         public static DataSource getDataSource(){
             return dataSource;
         }
     
     }
     ```

     

# Spring JDBC

* Spring框架提供的JDBC简单封装。提供了一个JDBCTemplate对象简化JDBC的开发
* 步骤
  * 导入jar包  (记得还导入mysql驱动)
  * 创建JdbcTemplate对象。依赖于数据源DataSource（数据库连接池）
    * JdbcTemplate template=new JdbcTemplate(ds);
  * 调用方法来完成crud操作
    * update()：执行DML语句 （增删改）
    * queryForMap（）：查询结果将结果集封装为Map集合，**只能查询结果为1个的语句**
    * queryForList（）：查询结果将结果集封装为List集合
    * query()：查询结果，将结果封装为JavaBean对象
      * query的参数：RowMapper接口
        * 一般我们使用BeanPropertyRowMapper实现类，可以完成数据到JavaBean的自动封装
        * 例：new BeanPropertyRowMapper<Person>(Person.class)
    * queryForObject（）：查询结果，将结果封装为对象（一般用于聚合函数的查询）

* ```java
   //1.导入jar包，加入library
          //2.创建JdbcTemplate对象，参数是数据库连接池
          JdbcTemplate jdbcTemplate=new JdbcTemplate(JDBCUtils_Druid.getDataSource());
          //3.调用方法
          String sql="update account set balance =100 where id=?";
          int result = jdbcTemplate.update(sql, 2);
  ```

* 练习：

  * **注意！JavaBean在封装时成员变量最好写成封装类型Integer Double等，否则在调用template进行封装时会出现，如果某个值为null就会报错**
  * 需求
    1. 修改数据
    2. 增加记录
    3. 删除记录
    4. 查询一定条件的结果，封装为map集合
    5. 查询所有记录，分装为list
    6. 查询所有记录，封装为emp对象的list集合
    7. 查询总记录数（count聚合函数）

  ```java
  public class demo04_test {
      @Test
      public void test01(){
          //查询结果以map的方式返回,**查询结果只能为1个**
          JdbcTemplate jdbcTemplate=new JdbcTemplate(JDBCUtils_Druid.getDataSource());
          Map<String, Object> stringObjectMap = jdbcTemplate.queryForMap("select*from account where id=1");
          System.out.println(stringObjectMap);
  
      }
      @Test
      public void test02(){
          JdbcTemplate jdbcTemplate=new JdbcTemplate(JDBCUtils_Druid.getDataSource());
          List<Map<String, Object>> list = jdbcTemplate.queryForList("select*from account");
          System.out.println(list);
      }
      @Test
      public void test03(){
          //遍历所有，将每行的结果封装为一个JavaBean
          JdbcTemplate jdbcTemplate=new JdbcTemplate(JDBCUtils_Druid.getDataSource());
          List<Person> personList = jdbcTemplate.query("select*from account", new RowMapper<Person>() {
              @Override
              public Person mapRow(ResultSet resultSet, int i) throws SQLException {
                  Person p = new Person();
                  p.setId(resultSet.getInt("id"));
                  p.setBalance(resultSet.getInt("balance"));
                  p.setName(resultSet.getString("name"));
                  return p;
              }
          });
          System.out.println(personList);
      }
      //简化版封装JavaBean
      @Test
      public void test04(){
          JdbcTemplate jdbcTemplate=new JdbcTemplate(JDBCUtils_Druid.getDataSource());
          List<Person> personList = jdbcTemplate.query("select*from account", new BeanPropertyRowMapper<Person>(Person.class));
          System.out.println(personList);
      }
      //查询记录条数
      @Test
      public void test05(){
          JdbcTemplate jdbcTemplate=new JdbcTemplate(JDBCUtils_Druid.getDataSource());
          Integer integer = jdbcTemplate.queryForObject("select count(*)from account", Integer.class);
          System.out.println(integer);
      }
  }
  ```

  