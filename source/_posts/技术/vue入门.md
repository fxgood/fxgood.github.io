---
title: vue入门
date: 2020-3-10
category: 技术
tag:
- web
---

## vue简介

* JavaScript框架
* 简化Dom操作
* 响应式数据驱动
  * 页面是由数据来生成的，数据改变，页面跟着改变

<!--more-->

## 第一个Vue程序

```html
<head>
    <title>vue1111</title>
</head>
<body>
    <div id='app'>
        { {message} }
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var app=new Vue({
            el:'#app',  //#是id选择器
            data:{
                message:"你好!"
            }
        })
    </script>

</body>
```

## el:挂载点

建议使用id选择器

只用用于双标签，但\<html>和\<body>除外，建议用于\<div>

## data:数据对象

```html
<head>
    <title>vue1111</title>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
</head>
<body>
    <div id='app'>
        <h2>{ {person.name} }</h2>
        <h2>{ {person.age} }</h2>
        <ul>
            <li>{ {subjects[0]} }</li>
            <li>{ {subjects[1]} }</li>
        </ul>
    </div>


    <script>
        var app=new Vue({
            el:'#app',  //#是id选择器
            data:{
                person:{
                    name:'余丰旭',
                    age:22,
                },
                subjects:['English','Math']
            }
        })
    </script>

</body>
```

# 本地应用

## vue指令

### v-text

* `v-text`指令的作用是：设置标签的内容（textContent）
* 默认写法会替换全部内容，使用**差值表达式{ {} }**可以替换指定内容
* 内部支持写表达式

```html
<head>
    <title>vue1111</title>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
</head>
<body>
    <div id='app'>
        <h2 v-text="'message'+'!'">大哥大</h2>
        <h2>{ {info+'!'} }大哥大</h2>
    </div>


    <script>
        var app=new Vue({
            el:'#app',
            data:{
                message:'what',
                info:'how'
            }
        })
    </script>

</body>
```

### v-html

* 设置标签的innerHTML

```html
<head>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
</head>
<body>
    <div id='app'>
        <p v-html='content'></p>
        <p v-text='content'></p>
    </div>

    <script>
        var app=new Vue({
            el:'#app',
            data:{
                content:"<a href='https://www.baidu.com'>百度官网</a>"
            }
        })
    </script>
</body>
```

### v-on

* 作用：为元素绑定事件
* 事件名不需要写`on`
* 指令可以简写为`@`
* 绑定的方法定义在`methods`属性中
* 方法内部通过`this`关键字就可以访问定义在`data`中的数据

### 案例：计算器

* 知识点
  * 创建Vue实例时：`el`（挂载点）,`data`（数据）,`methods`（方法）
  * `v-on`指令的作用是绑定事件，简写为`@`
  * 方法中通过`this`，关键字获取`data`中的数据
  * `v-text`指令的作用是：设置元素的文本值，简写为{ {} }
  * `v-html`指令的作用是：设置元素的`innerHTML`

***

### v-on补充

* 事件绑定的方法写成**函数调用**的形式，可以传入自定义参数
* 定义方法时需要定义**形参**来接收传入的实参
* 事件的后面跟上`.修饰符`可以对事件进行限制，如
  * @keyup.enter    回车按键松开
  * @keyup.left 左键松开
  *  @keyup.right    右键松开
  *  @keyup.up   上键松开
  *  @keyup.down 下键松开
  *  @keyup.delete   删除键松开
* `.enter`可以限制触发的按键为回车
* 事件修饰符有多种

***

### v-show

* 根据表达式的真假，让元素显示或者隐藏

***

### v-if

* 和v-show基本相同

***

### v-blind

* 修改属性
* 可以缩写为`:属性=`

***

### v-for

* 根据数据生成列表结构（响应式）
* 数组经常和`v-for`一起使用
* 语法是`(item,index) in 数据`
* item和index可以结合其他指令一起使用
* 数组长度的更新会同步到页面上，是响应式的

```html
<head>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
</head>
<body>
    <div id='app'>
        <ul>
            <li v-for="(item,index) in arr">
                第{ {index} }个数:{ {item} }
            </li>
        </ul>
        <br>
        <br>
        <ol>
            <li v-for="(item,index) in objArr">
                第{ {index+1} }个人的名字是:{ {item.name} }
            </li>
        </ol>
    </div>
    <script>
        var app=new Vue({
            el:'#app',
            data:{
                arr:[1,2,3,4,5],
                objArr:[
                    {name:'余丰旭'},
                    {name:'陈香玉'}
                ]
            }
        })
    </script>
</body>
```

***

### v-model

* 获取和设置表单元素的值（**双向数据绑定**）
* `v-model`指令的作用是便捷地设置和获取表单元素的值
* 绑定的数据会和表单元素**值**相关联
* 绑定的数据<——>表单元素的值

```html
<body>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <div id='app'>
        <input type="text" v-model="textContent" @keyup.enter='showMessage'>
        <br>
        <h2>{ {textContent} }</h2>
        <br>
        <h2 v-text='textContent'></h2>
        <br>
    </div>
    

    <script>
        var app=new Vue({
            el:"#app",
            data:{
                textContent:'你好鸭读研人~'
            },
            methods:{
                showMessage:function(){
                    alert(this.textContent)
                }
            }
        })
    </script>
</body>
```



***

### 案例：网页记事本

```html
<head>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <style>
        .item{
            border:solid 1px black;
            width: 200px;
        }
        #calculator{
            padding-left: 30%;
            border: 1px solid blue;
        }
    </style>
</head>
<body>
    <div id='calculator'>
        <h2 style="padding-left: 10%;">在线记事本</h2>
        <input style="margin-left: 7%;" type="text" v-model="currentInput" @keyup.enter="addItem(currentInput)">
        <ul>
            <li v-for="(item,index) in content" class="item">
                { {item} }
                <button @click="deleteItem(index)">x</button>
            </li>
            <li style="font-size: small;">
                一共{ {content.length} }个项目
                <button @click="clearAll">清空</button>
            </li>
        </ul>

    </div>

    <script>
        var calculator=new Vue({
            el:'#calculator',
            data:{
                content:[
                    '吃饭',
                    '洗脸'
                ],
                currentInput:'请输入任务'
            },
            methods:{
                deleteItem:function(index){
                    // delete this.content.shift()
                    this.content.splice(index,1)
                },
                addItem:function(input){
                    this.content.push(input)
                },
                clearAll:function(){
                    this.content=[]

                }
            }
        })
    </script>

</body>
```

## 网络应用

* Vue结合网络数据开发应用
* `axios`网络请求库
* `axios+vue`

***

### axios基本使用

* 功能强大的网络请求库
* `axios`必须先导入才能使用
* 使用`get`和`post`方法即可发送对应的请求
* `then`方法中的回调函数会在请求成功或者失败时触发
* 通过回调函数的形参可以获取响应内容，或者错误信息
* 用法
  * `<script src="https://unpkg.com/axios/dist/axios.min.js"></script>`
  * axios.get(地址?查询字符串).then(function(response){},function(err){})
  * axios.post(地址,{key:value,key2:value2}).then(function(response){},function(err){})

```html
<head>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
</head>
<body>
    <input type="button" value="get请求" class='get'>
    <input type="button" value="post请求" class='post'>

    <script>
        document.querySelector(".get").onclick=function(){
            axios.get("https://autumnfish.cn/api/joke/list?num=6")
            .then(function(response){
                console.log(response);
            },function(err){
                console.log(err)
            })
        }
        document.querySelector(".post").onclick=function(){
            axios.post("https://autumnfish.cn/api/user/reg",
            {username:'jackandhisfriends'})
            .then(function(response){
                console.log(response)
            },function(err){
                console.log(err)
            })
        }
    </script>
</body>
```

### axios+vue

* 网络应用的核心是：data中的一部分数据是从网络中获取的

* 保证编码在导入两个地址之后
* **坑**
  * axios回调函数中的**this**已经改变，无法访问到data中的数据‘
  * 解决方法：把this保存起来，回调函数中直接使用保存的this即可

```html
<head>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
</head>
<body>
    
    <div id='jokes'>
        <button @click="getJokes">获取笑话</button>
        <ul>
            <li v-for="item in jokes">
                { {item} }
            </li>
        </ul>
    </div>

    <script>
        var jokes=new Vue({
            el:'#jokes',
            data:{
                jokes:[]
            },
            methods:{
                getJokes:function(){
                    var that=this;
                    axios.get("https://autumnfish.cn/api/joke/list?num=3")
                    .then(function(response){
                        that.jokes=response.data.jokes
                    })
                },
            }
        })
    </script>
</body>
```