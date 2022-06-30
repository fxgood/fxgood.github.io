---
title: JavaScript
category: 技术
date: 2020-3-2
tag:
- web
---

# JavaScript简介

* 概念：一门客户端脚本语言
  * 运行在客户端浏览器中，每个浏览器都有javascript地解析引擎
  * 脚本语言：无需编译，直接就可以被浏览器解析执行
* 功能：
  * 可以来增强用户和html页面的交互。可以控制html元素，可以实现页面的动态效果，增强用户体验
* JavaScript发展史
  * 三足鼎立，略
  * ECMA（欧洲计算机制造商协会），制定出客户端脚本语言的标准：ECMAScript，统一了所有客户端脚本语言的编码方式
  * JavaScript=ECMAScript+JavaScript的（BOM+DOM)
  * ECMAScript：客户端脚本语言的标准

<!--more-->

***

# ECMAScript

## 基本语法

1. 与html结合方式

   * 内部JS
     * 定义<script>，标签体内容就是js代码
     * 定义<script>,通过src属性引入外部的js文件
     * `<script>`标签可以写在任意位置，但是影响加载顺序，一般放后面，否则会获取不到html对象
   * 外部JS

2. 注释

   * 单行注释 //
   * 多行注释 /*  */

3. 数据类型

   * 原始数据类型（基本数据类型）
     * number：数字。 正数/小数/NaN（not a number）
     * string：字符串。 字符/字符串  "abc" "a" 'abc'  没有字符的概念，不区分单双引号
     * boolean：true和false
     * null：一个对象为空的占位符
     * undefined：未定义。如果一个变量没有给初始化值则会被默认赋值为undefined
   * 引用数据类型：对象

4. 变量

   * 变量：一小块存储数据的内存空间

   * Java语言是强类型语言，而Javascript是弱类型语言，即申请空间时不规定类型 

   * 语法：

     * var 变量名 = 初始化值；

       ```html
       <script>
               var a=1;
               var b="我是一句话";
               var c=NaN;
               var d=true;
               var e=null;
               var f=undefined;
               document.write(a+"<br>");
               document.write(b+"<br>");
               document.write(c+"<br>");
               document.write(d+"<br>");
               document.write(e+"<br>");
               document.write(f+"<br>");</script>
       ```

   * typeof 运算符可以查看变量类型

     * 实际开发中不需要了解具体的类型，javascript是弱类型语言。但面试会问

5. 运算符

   * 跟java基本相同
   * 多了一个`===`全等于符号，比较时先判断类型，类型不同直接返回false（javaScript在其他类型的运算符比较时，如果类型不同会先转换类型再比较）
   * **注意**：再JS中，如果运算数不是运算符所要求的类型，js引擎会自动将运算数进行类型转换
     * string转number，按照字面值转。如果字面值不是数字，则转为NaN类型
     * boolean转number，true转1，false转0

6. 流程控制语句

   * 和java相同
   * JS中，switch语句可以接受任意的原始数据

7. 特殊语法

   * 语句以`;`结尾，一行只有一个语句，可以省略（不推荐）
   * 变量可以不使用`var`
     * 用：定义的是局部变量
     * 不用：定义的是**全局变量**（不建议在方法中不加var定义全局变量）

## 基本对象

1. Function对象：函数对象

   * 创建

     * ```html
       //形参前面的var可以省略
       function fun1(a,b){
       	alert(a+b);
       }
       ```

     * ```html
       var fun2=function(a,b){
       	alert(a+b);
       }
       ```

   * 属性

     * length：代表形参个数

   * 特点

     * 方法定义中，形参的类型不用写，返回值类型也不写
     * 方式是一个对象，如果定义名称相同的方法会覆盖
     * **在JS中，方法的调用只与方法的名称有关，和参数列表无关**
     * 在方法声明中有一个隐藏的内置对象（数组），arguments，封装所有的实际参数

2. Array：数组对象

   * 创建
     * var arr=new Array(元素列表);
     * var arr=new Array(默认长度);
     * var arr=[元素列表]
   * 方法
     * join()：将数组中的元素，按照指定的分隔符，拼接为字符串(默认是逗号)
     * push()：类似java中的add(),向数组尾部添加1个或更多元素
     * 其他的查看文档
   * 属性
     * length
   * 特点
     * JS中，数组元素的类型是可变的
     * JS中，数组长度可变（数组不会访问越界），**会自动扩容**

3. Date对象

   * 创建
     * var date=new Date();
   * 方法
     * toLocalString()：以当地的使用习惯返回时间
     * getTime()：获取毫秒值。和1970年1月1日之间的毫秒值差

4. Math对象

   * 创建：
     * 特点：Math对象不用创建，直接使用 
   * 方法
     * Math.PI ：返回Π值
     * Math.random()：返回[0,1)之间的一个随机数
     * Math.ceil()：对数进行向上取整
     * Math.floor()：对数进行向下取整
     * Math.round()：四舍五入
   * 属性
     * Math.PI：返回圆周率

5. Number

6. String

7. RegExp：正则表达式对象

   1. 正则表达式：定义字符串的组成规则

      * 单个字符：`[]`。
        * 如`[a-z]`表示a到z之间的一个字符
        * 如`[ab]`表示a或者b
        * `[a-zA-Z0-9_]`
        * 特殊符号代表特殊含义的单个字符
          * `\d`表示单个数字字符
          * `\w`单个单词字符`[a-zA-Z0-9_]`
      * 量词符号
        * `*`：表示0次或多次
        * `?`：表示出现0次或1次
        * `+`：表示出现1次或多次
        * 如`\w*`表示一个字符串，由单个字符构成，出现0次或多次
        * `{m,n}`表示最多 m<=数量<=n
          * `\w{6,12}`长度为6到12的仅由单词字符组成的字符串
          * `{,n}`：m缺省表示最多n次
          * `{m,}`：最少m次
      * 开始结束符号
        *  `^`：开始
        * `$`：结束
        * 只用^表示以该正则开头，只用$表示以该正则结尾，^xxx$表示字符串须严格匹配正则

   2. 正则对象

      1. 创建

         * var reg=new RegExp("正则表达式")
         * var reg=/正则表达式/  （不要加双引号）

      2. 方法

         * `test(参数)`：验证字符串是否符合正则表达式

         * ```html
               <script>
                   var reg1=new RegExp("\\w{2,4}");
                   var reg2=/\w{0,4}/;
           
                   /*使用test方法*/
                   var username="ab";
                   document.write(reg2.test(username));
               </script>
           ```

8. Global
   1. 特点：全局对象，这个Global中封装的方法不需要对象就可以直接调用
   2. 方法
      * encodeURL():url编码
      * decodeURL()：url解码
      * encodeURLComponent()：url编码
      * decodeURLComponent()：url节码
      * **后面的两种，编码出来的字符更多**
      * parseInt()：将字符串转数字
        * 逐一判断每个字符是否是数字，直到不是数字位置，将前边的数字部分转为number
      * isNaN（）：判断一个值是否是NaN
        * NaN六亲不认，连自己都不认。NaN参与的比较结果全部为false
      * eval()：将字符串转为javaScript代码来执行

# BOM

## 简单学习

1. 概念：Browser Object Model 浏览器对象模型

   * 将浏览器的各个组成部分封装成对象

2. 组成

   * Window：窗口对象

     * 特点：不需要创建对象， window.方法名() 或直接写方法名()  如alert（）

     * 方法

       * 与弹出框有关的方法

         * alert()：弹出警告框
         * confirm()：弹出确认框
           * 用户点击确定，返回true
           * 用户点击取消，返回false
         * prompt():显示一个用户输入框，**现在不常用**

       * 与打开关闭有关的方法

         * open()：返回一个新的window对象

         * close()：谁调用，关闭谁

         * ```html
           <body>
               <input id="button1" type="button" value="点击以打开一个新的标签页">
               <input id="button2" type="button" value="点击以关闭当前的页面">
           <script>
               var button1=document.getElementById("button1");
               var button2=document.getElementById("button2");
               var newWindow;
               button1.onclick=function () {
                   newWindow=open("http://www.baidu.com");
               }
               button2.onclick=function () {
                   if(newWindow==null)
                       close();
                   else
                       newWindow.close();
               }
           </script>
           </body>
           ```

       * 与定时器相关的方法

         * setTimeout()：在指定毫秒值之后执行事件

           * 第一个参数：js代码或方法对象
           * 毫秒值
           * 返回值：唯一标识，用于取消定时器

           ```html
           <script>
           
               //setTimeout("f();",1000);
              /* setTimeout(f,1000);
               function f() {
                   alert("1秒中过去了")
               }*/
               var id=setInterval(f1,1000);
               var i=1;
               function f1() {
                   if(i<=3)
                       document.write("第"+(i++)+"秒...<br>");
                   else
                       clearInterval(id);
               }
           </script>
           ```

           

         * clearTimeout()：取消由setTimeout（）方法设置的timeout

         * setInterval()：循环执行的定时器

         * clearInterval():取消之

     * 属性

       1. 获取其他BOM对象
          1. history
          2. location
          3. navigator
          4. screen
       2. 获取DOM对象
          1. document

   * Navigator：浏览器对象 **不讲解**

   * Screen：显示器屏幕对象 **不讲解**

   * History：当前页面历史记录对象

     * 创建
       * window.history 或省略window
     * 方法
       * back()：加载history列表中的前一个url
       * forward（）：加载history列表中的下一个url
       * go()：加载history列表中的某个具体页面
         * 参数为正数，前进n个历史记录
         * 参数为负数，后退n个历史记录
     * 属性
       * length：返回当前窗口历史列表中的url数量

   * Location：地址栏对象

     * 创建：window.location （可以省略window）
     * 方法
       * reload()：刷新
     * 属性
       * href	设置或返回完整的url（**可以用来跳转网页**）

     ```html
     <head>
         <meta charset="UTF-8">
         <title>倒计时跳转</title>
         <style>
             p{
                 text-align: center;
             }
             span{
                 color: red;
             }
         </style>
     </head>
     <body>
         <p>
             <span id="time">5</span>秒之后跳转
         </p>
     <script>
         var n=4;
         var time=document.getElementById("time");
         function f1() {
             if(n>=1)
                 time.innerHTML=n--;
             else
                 location.href="http://www.baidu.com";
         }
         setInterval(f1,1000);
     </script>
     </body>
     ```

     

# DOM

## 简单学习

* DOM：Document Object Model 文档对象模型
  * 将标记语言文档的各个组成部分封装为对象，对标记语言文档进行CRUD的动态操作
* 功能：控制html文档的内容
  * 即增删改查
  * 代码：获取页面的标签（元素）对象 Element
    * document.getElementById("id值")：通过元素的id获取元素对象   **记得加双引号**
  * 操作对象
    * 设置其属性
      * 明确获取的对象是哪一个
      * 查看API文档，找其中有哪些属性可以设置
    * 修改标签体内容
      * 属性：innerHTML

## 事件的简单学习

* 功能：某些组件被执行了某些操作后，触发某些代码的执行

* 如何绑定事件

  * 直接在html标签上，指定事件的属性（操作），属性就是js代码

    * 事件：onclick：单击事件

  * 通过js获取元素对象，指定事件属性，设置一个函数

    * ```html
      function f1() {
              alert("我被点了");
          }
          var img1=document.getElementById("img1");
          img1.onclick = f1;
      ```

    * **注意调用函数的时候不加括号，因为他是一个对象**

* 案例

  ```html
  <body>
      <img src="../img/future.jpg" id="img1">
  <script>
      var img1=document.getElementById("img1");
      var flag=true;
      img1.onclick=function () {
         if(flag){
             img1.src="../img/验证码1.png";
             flag=false;
  
         }
         else{
             img1.src="../img/future.jpg";
             flag=true;
         }
      }
  </script>
  </body>
  ```

  

## DOM对象

W3C DOM 标准被分为3个不同的部分

* 核心DOM
  * Node对象
    * 是其他5个的父对象
    * 所有的dom对象都可以认为是一个Node（节点）
    * 方法：
      * CRUD dom树
        * appendChild()：向节点的子节点列表的尾部添加新的子节点
        * removeChild()：删除（并返回）当前节点的指定子节点
        * replaceChild()：用新节点替换一个子节点
      * 属性
        * parentNode：返回当前节点的父节点
  * Document：文档对象
    * 创建：可以用window对象来获取
      1. window.document
      2. 直接document
    * 方法
      * 获取Element对象
        * getElementById()
        * getElementsByTagName()：根据元素名称获取元素对象们。返回的是数组
        * getElementsByClassName()：根据class属性值来获取元素对象们
        * getElementsByName()：根据name属性值来获取元素们
      * 创建其他的DOM对象
        * createElement()
        * createAttribute(name) （如table）
    * 属性
  * Element：元素对象
    * 通过document来获取和创建
    * 方法
      * removeAttribute():删除属性
      * setAttribute()：设置属性
  * Attribute：属性对象
  * Text：文本对象
  * Comment：注释对象
* XML DOM
* HTML DOM
  1. 标签体的设置和获取：innerHTML
  2. 使用html元素对象的属性
  3. 控制元素样式
     1. 第一种方式
        * `div1.style.border="solid red 1px";`
        * 遇到类似font-size的则改写为fontSize`div1.style.fontSize="20px";`
     2. 第二种方式：通过控制`className`在CSS中控制样式

**动态删除添加表格**

 ```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        #table{
            margin:auto;
            width: 500px;

        }
        div{
            margin: 50px;
            text-align: center;
        }
        th,td{
            text-align: center;
            border: 1px solid;
            border-radius: 5px;
        }
        .btn{
            text-align: center;
            border: 1px solid;
            border-radius: 5px;
        }


    </style>
</head>
<body>
<div id="div1">
    <input id="input1" type="text" placeholder="编号">
    <input id="input2" type="text" placeholder="姓名">
    <input id="input3" type="text" placeholder="性别">
    <input id="btn1" type="button" value="添加">
</div>

<table id="table">
    <tr id="tr1">
        <th>编号</th>
        <th>姓名</th>
        <th>性别</th>
        <th>操作</th>
    </tr>
</table>
<script>
    /*发生错误时，浏览器F12 console查找错误*/
    //1.获取输入内容
    document.getElementById("btn1").onclick=function () {
        var code=document.getElementById("input1").value;
        var name=document.getElementById("input2").value;
        var gender=document.getElementById("input3").value;
        //获取table
        var table=document.getElementById("table");
        //创建三个td
        var td_code=document.createElement("td");
        var textNode_code=document.createTextNode(code);
        td_code.appendChild(textNode_code);
        var td_name=document.createElement("td");
        var textNode_name=document.createTextNode(name);
        td_name.appendChild(textNode_name);
        var td_gender=document.createElement("td");
        var textNode_gender=document.createTextNode(gender);
        td_gender.appendChild(textNode_gender);
        //创建一个tr
        var tr=document.createElement("tr");
        tr.appendChild(td_code);
        tr.appendChild(td_name);
        tr.appendChild(td_gender);
        //创建一个删除按钮
        var btn_delete=document.createElement("input");
        btn_delete.setAttribute("type","button");
        btn_delete.setAttribute("value","删除");
        btn_delete.setAttribute("class","btn");

        btn_delete.onclick=function () {
            //删除本行 tr
            table.removeChild(tr);
        }
        tr.appendChild(btn_delete);
        //将tr放入表格
        table.appendChild(tr);
    }
    //2.添加到表中
    //3.删除按钮
</script>
</body>
</html>
 ```

**案例动态删除添加表格_innerHTML简化版**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        #table{
            margin:auto;
            width: 500px;

        }
        div{
            margin: 50px;
            text-align: center;
        }
        th,td{
            text-align: center;
            border: 1px solid;
            border-radius: 5px;
        }
        .btn{
            text-align: center;
            border: 1px solid red;
            border-radius: 5px;
        }


    </style>
</head>
<body>
<div id="div1">
    <input id="input1" type="text" placeholder="编号">
    <input id="input2" type="text" placeholder="姓名">
    <input id="input3" type="text" placeholder="性别">
    <input id="btn1" type="button" value="添加">
</div>

<table id="table">
    <tr id="tr1">
        <th>编号</th>
        <th>姓名</th>
        <th>性别</th>
        <th>操作</th>
    </tr>
</table>
<script>
    /*发生错误时，浏览器F12 console查找错误*/
    //1.获取输入内容
    function del(obj){
        var table=obj.parentNode.parentNode.parentNode;
        var tr=obj.parentNode.parentNode;
        table.removeChild(tr);
    }
    document.getElementById("btn1").onclick=function () {
        var code=document.getElementById("input1").value;
        var name=document.getElementById("input2").value;
        var gender=document.getElementById("input3").value;

        var table=document.getElementById("table");
        table.innerHTML+="<tr>\n" +
            "        <td>"+code+"</td>\n" +
            "        <td>"+name+"</td>\n" +
            "        <td>"+gender+"</td>\n" +
            "        <td><a href='javascript:void(0);'onclick='del(this)' >删除</td>\n" +
            "    </tr>"
    }
    //2.添加到表中
    //3.删除按钮
</script>
</body>
</html>
```