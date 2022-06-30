---
title: HTML与CSS学习笔记
date: 2020-4-29
category: 技术
tag:
- web
excerpt: 记录html和css的学习过程，主要是各种标签的作用。
---

# JavaWeb概述

* JavaWeb
	* 使用Java开发的基于互联网的项目
* 软件架构
	* B/S
	* C/S
* B/S架构详解
	* 静态资源：使用静态网页开发技术发布的资源
		* 特点：
			* 所有用户访问，得到的结果相同 
			* 如：文本、图片、视频，HTML，CSS，JavaScript
			* 如果用户请求的是静态资源，服务器会直接将静态资源发送给浏览器，浏览器中内置了静态资源的解析引擎，可以展示静态资源
	* 动态资源：使用动态网页技术即时发布的资源
		* 特点：
			* 所有用户访问，得到的结果可能不同
			* 如：jsp/servlet,php,asp...
			* 如果用户请求的是动态资源，那么服务器会执行动态资源，转化为静态资源，再发送给浏览器	
* 我们要学习动态资源，**必须先学习静态资源**
* 静态资源：
	* HTML：用于搭建基础网页，用于展示页面内容
	* CSS：用于美化页面，布局页面
	* JavaScript（盲猜做业务流用不到）：控制页面元素，让页面有一些动态效果
***
# HTML

## HTML基础

1.概念：是最基础的网页开发语言

* 超文本：用超链接的方法，把不同空间的文字信息组织在一起的网状文本
* 标记语言：由标签构成的语言

2.快速入门
* 语法：
	* html文档后缀.html 或.htm
	* 标签分为
		* 围堵标签：如<html></html>
		* 自闭和标签：开始标签和结束标签在一起，如<br/>
	* 标签可以嵌套  
	* 在开始标签中可以定义属性。属性是由键值对构成，值需要用引号（单双都可）引起来
	* html标签不区分大小写，建议小写

3.标签（在帮助文档里可以查到具体的）

 1. 文件标签：构成html最基本的标签

    * html：html文档的根标签
    * head： 头标签。用于指定html文档的一些属性。引入外部资源
    * title：标题标签
    * body：体标签
    * <!DICTYPE html>：html5中定义该文档是html文档

 2. 文本标签：和文本有关的标签

    * \<!--  --> ：注释

    * \<h1>  to \<h6> ：标题标签
    * \<p>：段落标签   （paragraph段落）
    * \<br>：换行标签
    * \<hr/>:显示一条水平线 （自闭和标签）（horizontal rule 水平分割线）
      * \<hr color='red' width='200' size='10' align="left"/> 
      * 可以设置属性来改变样式，不过了解即可，**html5不推荐使用**，后面会用CSS
    * \<b>:字体加粗
    * \<i>：字体斜体
    * \<font>：字体标签  **不推荐使用，已废弃**

 3. 图片标签：

    * \<img/>：**自闭和标签**，展示图片
      * 只需记得`src`给出图片路径即可
      * 相对路径 `./`代表当前路径 可以省略 `../`表示上一级目录
      * `<img src="image/timg.jpg" align="right" alt="美丽的风景"/>`
      * alt代表图片加载失败时显示的文字

 4. 列表标签：

    * 有序列表

      * `ol`:(order list)

      * `li`:

        ```html
        //type省略默认按123..排序
        //start决定从什么开始数
        <ol type="a" start='3'>
                <li>起床</li>
                <li>刷牙</li>
                <li>吃饭</li>
            </ol>
        ```

    * 无序列表

      * `ul`：可以修改type
      * `li`

 5. 链接标签：

    * `a`：定义一个超链接

      ```html
      <a href="http://www.baidu.com">点我 </a>
      ```

      * `href`：指定URL（统一资源定位符）
      * `target`：`-self`指在本页面打开 `_blank`指在一个新的页面打开

    * 可以用`<a>`标签包裹图片

6. div和span标签（了解，将来和CSS结合）
   * `span`：文本信息在一行展示，行内标签
   * `div`：每一个div占满一整行，会自动换行。块级标签
7. 语义化标签：html5中为了提高程序的可读性，提供了一些标签，结合CSS一起使用
   * `header`
   * `footer`
8. 表格标签
   * `table`：定义表格
     * 一下属性html5基本都已废弃，改用CSS控制
     * `width`：
     * `border`：
     * `cellpadding`：定义内容与单元格之间的距离
     * `cellspacing`：定义单元格之间的距离，0则会变为线
     * `bgcolor`：背景色
   * `tr`：定义行
     * `bgcolor`：背景色
     * `align`：内部文本对齐方式
   * `td`：定义单元格
     * `colspan`：合并列	
     * `rowspan`：合并行
   * `th`：定义表头单元格
   * `caption`：定义表格标题
   * `thead`：表示表格的头部分
   * `tbody`：表示表格的体部分
   * `tfoot`：表示表格的脚部分

## html表单标签

**表单**：用于采集用户输入的数据的。用于和服务器进行交互

**使用的标签**：form 可以定义一个范围，范围代表采集用户数据的范围

**属性**

`action`：指定提交数据的URL

`method`：指定提交方式

* `get`：
  * 请求参数会在地址栏中显示。会封装到请求行中
  * 请求参数长度有限制
  * 不安全

* `post`：
  * 请求参数**不会**在地址栏中显示，会封装在请求体中（http协议）
  * 请求参数的大小**没有限制**
  * 较为安全

表单中的数据要想被提交，必须指定其name属性

**例：**

```html
<form action="###" method="get">
    用户名：<input name="username"><br>
    密码：<input name="password"><br>
    <input type="submit" value="登录">
</form>
```

**表单项标签**

* `input`：可以通过type属性值，改变元素展示的样式
  * type属性
    * `text`
      * `placeholder`：指定输入框提示信息
    * `password`
    * `radio`：单选框
      * **注意**：要想使多个单选框实现单选，他们的name必须相同
      * 一般会给每个radio提供value值，指定其被选中后提交的值
      * `checked`，可以指定默认值
    * `checkbox`：复选框
      * `checked`，可以指定默认值
    * `file`：文件选择框
    * `hidden`：隐藏域，用于提交一些信息
    * **按钮**
      * `submit`：提交
      * `button`: 配合javascript使用的按钮
      * `image`：图片提交按钮 （src指定图片路径）
* `label`：指定输入项的文字描述信息
  * 注意：label的for属性一般和input的id属性值对应。对应后点击label区域，会让input输入框获取焦点

* `select`：下拉列表
  * `option`：指定列表项
* `textarea`：文本域
  * `cols`：指定列数，每一行有多少字符
  * `rows`：默认多少行

**案例**

```html
</head>
<body>
    <form method="get">
        <label for="username">用户名：</label><input type="text" name="username"  placeholder="请输入用户名" id="username"><br>
        <label for="password">密码：</label><input type="password" name="password" placeholder="请输入密码" id="password"><br>
        性别：<input type="radio" name="gender" value="male" checked> 男
             <input type="radio" name="gender" value="female"> 女  <br>
        爱好：<input type="checkbox" name="hobby" value="game"> 打游戏
             <input type="checkbox" name="hobby" value="learning" checked> 学习
             <input type="checkbox" name="hobby" value="sports"> 运动  <br>
        取色器：<input type="color" name="color"><br>
        生日：<input type="date" name="birthday"><br>
        邮箱：<input type="email" name="email"><br>
        年龄：<input type="number" name="age"><br>
        籍贯：<select name="homecity">
                <option>请选择</option>
                <option value="0">北京</option>
                <option value="1">南京</option>
            </select><br>
        自我介绍：<br><textarea name="selfIntroduction" rows="3" cols="20"></textarea><br>
        <input type="submit" value="登录">
    </form>
</body>
```



**案例2**

```html
<body>
    <!--定义表单-->
    <form action="###" method="post">
        <table border="1" align="center" width="500">
            <!--第一行-->
            <tr>
                <td><label for="username">用户名</label></td>
                <td><input id="username" type="text" name="username" placeholder="用户名"></td>
            </tr>
            <!--第二行-->
            <tr>
                <td>密码</td>
                <td><input type="password" name="password" placeholder="密码"></td>
            </tr>
            <!--第三行-->
            <tr>
                <td>Email</td>
                <td><input type="email" name="email" placeholder="邮件"></td>
            </tr>
            <!--第四行-->
            <tr>
                <td>姓名</td>
                <td><input type="text" name="name" placeholder="姓名"></td>
            </tr>
            <!--第五行-->
            <tr>
                <td>手机号码</td>
                <td><input type="text" name="phoneNum" placeholder="手机号码"></td>
            </tr>
            <!--第六行-->
            <tr>
                <td>性别</td>
                <td>
                    <input type="radio" name="gender" checked>男
                    <input type="radio" name="gender">女
                </td>
            </tr>
            <!--第七行-->
            <tr>
                <td>出生日期</td>
                <td><input type="date" name="birthday"></td>
            </tr>
            <!--第八行-->
            <tr>
                <td>验证码</td>
                <td>
                    <input type="text" name="vcode" placeholder="验证码">
                    <img src="../image/验证码.jpg" align="right" width="150" height="50">
                </td>
            </tr>
            <!--注册按钮-->
            <tr>
                <td colspan="2" align="center"><input type="submit" value="注册"></td>
            </tr>
        </table>
    </form>
</body>
```

# CSS

## 基本概念

1. 概念

   * CSS：页面美化和布局控制

   * Cascading style sheets 层叠样式表
   * **层叠**：多个样式可以作用在同一个html的元素上，同时生效

2. 使用CSS的好处
   * 功能强大
   * 将内容展示和样式控制分离
     * 降低耦合度，解耦
     * 让分工协作更容易，
     * 可以提高开发效率

## CSS与html结合方式

### 内联样式

* 标签内使用style属性指定css代码
* 如`<div style="color: crimson">IloveYoubaby</div>`
* 未实现内容与样式的分离，**不推荐使用**

### 内部样式

* 在head标签内，定义style标签，style标签的标签体内容就是css代码

* ```html
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
      <style>
          div{
              color: blue;
          }
      </style>
  
  </head>
  <body>
      <div>Legion<br>what's up?</div>
  </body>
  ```

* 缺点：如上所有div使用同一的样式

### 外部样式

* 定义css资源文件
* 在head标签内，定义link标签，引入外部的资源文件

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" href="../CSS资源/a.css">
</head>
<body>
    <div>计算机专硕复试</div>
</body>
```

```html
//css资源文件
div{
    color: green;
}
```

***

### 小结

* 1方式不常用，后期常用2，3
* 第三种格式引入css资源还可以携程

```html
<style>
    @import "##css位置##"
</style>
```

***

## CSS基本语法

### 格式

```html
选择器{
	属性名1:属性值;
	属性名2:属性值;
	...
}
```

**选择器**：筛选具有相似特征的元素

**注意**：每一对属性需要使用`;`分开，最后一对可以不用

***

### 选择器

分类：

1. 基础选择器

   1. id选择器：建议在一个html页面中id值唯一

      ```html
      <head>
          <meta charset="UTF-8">
          <title>Title</title>
          <style>
              #d1{
                  color: crimson;
              }
              #d2{
                  color: aqua;
              }
          </style>
      </head>
      <body>
          <div id="d1">我是第一行</div>
          <div id="d2">我是第二行</div>
      </body>
      ```

      

   2. 元素选择器：选择具有相同标签名称的元素
      （id选择器的优先级更高，会覆盖元素选择器）

      ```html
      <head>
          <meta charset="UTF-8">
          <title>Title</title>
          <style>
              div{
                  color: aquamarine;
              }
          </style>
      </head>
      <body>
          <div>我是第一行</div>
          <div>我是第二行</div>
      </body>
      ```

      

   3. 类选择器：选择具有相同class属性值的元素

      * 语法：.class属性值{}

      * 注意：优先级：id选择器>类选择器>元素选择器

        ```html
        <head>
            <meta charset="UTF-8">
            <title>Title</title>
            <style>
                .c1{
                    color: crimson;
                }
                .c2{
                    color: blue;
                }
            </style>
        </head>
        <body>
            <p class="c1">
                这是第一段
            </p>
        
            <div class="c2">这是一行</div>
            <div class="c1">这是二行</div>
        
        </body>
        ```

        

2. 扩展选择器

   1. `*`：选择所有元素

   2. 并集选择器：

      * 语法：选择器1，选择器2{ }

   3. 子选择器：筛选选择器1元素下的选择器2元素

      * 语法：选择器1 选择器2{ }

      ```html
      <head>
          <meta charset="UTF-8">
          <title>Title</title>
          <style>
              div p{
                  color: crimson;
              }
          </style>
      </head>
      <body>
          <div>
              <p>这是第一行</p>
              <p>这是第二行</p>
          </div>
      </body>
      ```

   4. 父选择器：筛选选择器2上的父元素选择器1

      * 语法：选择器1>选择器2{}

      ```html
      <head>
          <meta charset="UTF-8">
          <title>Title</title>
          <style>
              div>p{
                  border: 1px solid;
              }
          </style>
      </head>
      <body>
          <div>
              <p>我是div内的p</p>
          </div>
          <p>我是div外面的p</p>
      </body>
      ```

   5. 属性选择器：选择元素名称，属性名=属性值的元素
      语法：元素名称[属性名=“属性值”]{}

      ```html
      <head>
          <meta charset="UTF-8">
          <title>Title</title>
          <style>
              input[type="password"]{
                  background: crimson;
              }
          </style>
      </head>
      <body>
          <input type="text">
          <input type="password">
      </body>
      ```

   6. 伪类选择器：选择一个元素具有的状态

      * 语法：元素：状态{}

      * 如：<a>

        * link：初始化的状态
        * visited：被访问过的状态
        * active：正在访问的状态
        * hover：鼠标悬浮状态

        ```html
        <head>
            <meta charset="UTF-8">
            <title>Title</title>
            <style>
                /*控制超链接初始状态*/
                a:link{
                    color: pink;
                }
                /*控制超链接悬浮状态下的颜色*/
                a:hover{
                    color: yellow;
                }
                /*控制访问过的超链接的颜色*/
                a:visited{
                    color: gray;
                }
            </style>
        </head>
        <body>
            <a href="http://www.4399.com" target="_blank">click me to find a new world!!</a>
        </body>
        ```

        

### 属性

1. 字体、文本

   * font-size：字体大小
   * color：文本颜色
   * text-align：对齐方式
   * line-height：行高

2. 背景

   * background
     `background: url("../image/timg.jpg") no-repeat;`

3. 边框

   * border：设置边框，复合属性

4. 尺寸

   * width：
   * height：

5. 盒子模型：控制布局

   * margin：外边距

   * padding：内边距

     * 默认情况会影响整个盒子大小
     * 设置box-sizing: border-box 可以解决

   * float：浮动

     * left
     * right

     ```html
     <head>
         <meta charset="UTF-8">
         <title>Title</title>
         <style>
             div{
                 border: 1px solid red;
             }
             .d1{
                 height: 500px;
                 width: 500px;
                 margin: 300px;
                 padding: 100px;
             }
             .d2{
                 height: 200px;
                 width: 200px;
               /*  margin: 300px;
                 padding: 100px;*/
             }
             .d3{
                 float: left;
             }
             .d4{
                 float: left;
             }
             .d5{
                 float: right;
             }
         </style>
     </head>
     <body>
         <div class="d3">1111</div>
         <div class="d4">2222</div>
         <div class="d5">3333</div>
         <br>
         <div class="d1">
             <div class="d2"></div>
         </div>
     
     
     </body>
     ```

     

案例

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>登录</title>
    <style>
        *{
            margin:0px;
            padding: 0px;
            /*设置后不会因为边距改变元素大小*/
            box-sizing: border-box;
        }
        body{
            background: url("../image/future.jpg") center no-repeat;
            background-size: cover;
        }
        .layout{
            width: 900px;
            height: 500px;
            border: 8px solid #EEEEEE;
            background-color: white;
            /*水平居中*/
            margin: auto;
            margin-top: 30px;
        }
        .left{
            /*border: 1px red solid;*/
            float: left;
            width: 200px;

        }
        .left p:first-child{
            font-size: 20px;
            color: #FFD026;
        }
        .left p:last-child{
            font-size: 20px;
            color: #A6A6A6;
        }
        .mid{
            /*border: 1px red solid;*/
            float: left;
            width: 450px;
            padding-top: 10px;

        }
        .right{
            /*border: 1px red solid;*/
            float: right;
            margin:15px;
        }
        .right p:only-child{
            font-size: 15px;
        }
        .right p a{
            color: pink;
        }
        .td_left{
            width: 100px;
            text-align: right;
            height: 45px;

        }
        .td_right{
            padding-left: 30px;
            padding-top: 10px;
        }
        #username,#password,#email,#name,#phoneNum,#birthday,#vcode{
            width: 200px;
            height: 30px;
            border-radius: 7px;
            border: 1px solid gray;
            padding-left: 10px;
        }
        #vcode{
            width: 120px;
        }
        #img_check{
            /*width: 50px;*/
            height:30px;
            /*设置水平居中*/
            vertical-align: middle;
        }
        #register{
            width: 150px;
            height: 40px;
            background-color: bisque;
            border: 1px ;
            margin-top: 20px;

        }


    </style>

</head>
<body>
    <div class="layout">
        <div class="left">
            <p>新用户注册</p>
            <p>User Register</p>
        </div>

        <div class="mid">
            <div class="form">
                <form action="###" method="post">
                    <table >
                        <!--第一行-->
                        <tr>
                            <td class="td_left"><label for="username">用户名</label></td>
                            <td class="td_right"><input id="username" type="text" name="username" placeholder="用户名"></td>
                        </tr>
                        <!--第二行-->
                        <tr>
                            <td class="td_left">密码</td>
                            <td class="td_right"><input type="password" id="password" name="password" placeholder="密码"></td>
                        </tr>
                        <!--第三行-->
                        <tr>
                            <td class="td_left">Email</td>
                            <td class="td_right"><input type="email" id="email" name="email" placeholder="邮件"></td>
                        </tr>
                        <!--第四行-->
                        <tr>
                            <td class="td_left">姓名</td>
                            <td class="td_right"><input type="text" id="name" name="name" placeholder="姓名"></td>
                        </tr>
                        <!--第五行-->
                        <tr>
                            <td class="td_left">手机号码</td>
                            <td class="td_right"><input type="text" id="phoneNum" name="phoneNum" placeholder="手机号码"></td>
                        </tr>
                        <!--第六行-->
                        <tr>
                            <td class="td_left">性别</td>
                            <td class="td_right">
                                <input type="radio" name="gender" checked>男
                                <input type="radio" name="gender">女
                            </td>
                        </tr>
                        <!--第七行-->
                        <tr>
                            <td class="td_left">出生日期</td>
                            <td class="td_right"><input type="date" id="birthday" name="birthday"></td>
                        </tr>
                        <!--第八行-->
                        <tr>
                            <td class="td_left">验证码</td>
                            <td class="td_right">
                                <input type="text"id="vcode" name="vcode" placeholder="验证码">
                                <img id="img_check" src="../image/验证码1.png"  >
                            </td>
                        </tr>
                        <!--注册按钮-->
                        <tr>
                            <td  colspan="2" align="center"><input id="register" type="submit" value="注册"></td>
                        </tr>
                    </table>
                </form>
            </div>
        </div>

        <div class="right">
            <p>已有账号?<a href="http://www.baidu.com">立即注册</a></p>
        </div>

    </div>

</body>
</html>
```