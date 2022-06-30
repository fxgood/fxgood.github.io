---
title: java编程中遇到的问题
category: Java
---



# 字符串相关

- 使用String[]arr=str.split(",")，如果两个分隔符相邻，则会分割出空字符串

- 数字加字符串转为字符串的原理

```java
这里的加号是属于运算符重载。Java在语法层面不允许用户自行重载运算符，但是内置了两个重载操作符，即字符串操作过程中的+和+=。字符串的加号与数字类型加号相比有一定区别，只满足结合律，不满足交换律。编译器在处理+时，如果发现加号两侧都是字符串字面量或常量（常量会被编译器直接转换为字面量），则会直接将两个字面量拼接，用拼接后的字符串替换原来的表达式。如果发现加号两侧不都是字符串字面量常量，但是至少有一侧是字符串类型，则会将+号转换为StringBuilder的append方法，对不是字符串类型的数值调用String.valueOf方法，然后在运行时再进行求值。如果在循环中处理加号，则会产生多个StringBuilder对象，因此不要在循环中使用加号进行字符串拼接，而是应该自己手动在循环外创建StringBuilder对象。

作者：程序员吉森
链接：https://www.zhihu.com/question/456036161/answer/1849811948
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



# 数组相关

- Arrays.copyOf(arr,[len])可以复制数组，可以指定长度
- Arrays.copyOfRange(T[] arr,int from,int to)，复制数组，可以指定范围，左闭右开



# 集合相关

- int数组转ArrayList\<Integer>
  - `List<Integer>list=Arrays.stream(pushed).boxed().collect(Collectors.toList());`
  - boxed表示进行装箱
- ArrayList\<Integer>转int数组，目前来看只能手动for循环，因为ArrayList的toArray函数只能用于对象数组。
- ArrayDeque线性双端队列的性能非常好，底层使用数组实现的循环队列，没有固定的开头和结尾。因为无论是双端队列还是栈，都推荐使用它。
- PriorityQueue默认是小根堆，如果要建大根堆，可以传入lambda表达式
  - `PriorityQueue<Integer> que=new PriorityQueue<>((x,y)->(y-x));   //大根堆`

## Deque

双端队列是在可以在头尾进行删除插入操作的数据结构

实现类有ArrayDeque,LinkedList

所谓的头和尾,就是普通队列的取数据的位置(头)和插入数据的位置(尾)

## Map

- 如果遍历map.keySet(),然后对Map自身进行修改,会报错
  - 原因在于,keySet()的内容和Map自身是绑定的,修改一个另一个也会动
  - 而java迭代器过程中不能修改数据,否则会触发`fail-fast`
- 如果想将两个数或者更多作为key,**可以采用拼接字符串作为键**

# Stream API

## 案例

### 案例1

[leetcode179 最大数](https://leetcode.cn/problems/largest-number/)

注意基本数据类型必须装箱后才能使用Comparator

```java
class Solution {
        public String largestNumber(int[] nums) {
        String res = Arrays.stream(nums)
                .boxed()
                .sorted((a, b) -> -("" + a + b).compareTo("" + b + a))
                .map(Object::toString)
                .collect(Collectors.joining(""));
        if(res.charAt(0)=='0')
            return "0";
        return res;
    }
}
```

### 案例2

[leetcode136 只出现一次的数字](https://leetcode.cn/problems/single-number/)

```java
class Solution {
    public int singleNumber(int[] nums) {
        return Arrays.stream(nums).reduce((a,b)->a^b).getAsInt();
    }
}
```



# 输入输出相关

- 控制小数点的方式和C++差不多`int ans=3;System.out.printf("%.1f",(double)ans/2);`

- 不知道输入数据的数量时,如何进行输入`while(sc.hasNext())`

## next和nextLine

- **nextInt,或者next,会先先跳过无效的内容(空格 注意next也是,回车),直到有效内容出现.**
  - 然后有效输入结束后的东西均会留在缓冲区(比如空格,或回车)
  - 注意,如果读取的是连续的回车,那么next会全部跳过,直到出现有效内容
- **nextLine()**会读取缓冲区中的所有内容,直到出现回车(如果只有回车,那么读取的是空字符串)
  - 然后会消耗掉回车(否则下一个nextLine岂不是读不到东西了?)

- 因此读入数字或字符串,若以回车结尾的话,紧接着再nextLine,就会读出空字符串
- 结论是,如果nextLine前边有数字+回车,那么需要用一个额外的nextLine吸收回车!

# 正则表达式相关

- 分割字符串如果开头和结尾有分隔符,那么**不会**分割出空字符串!
- 连续两个分隔符放在一起,会切割出空字符串

# 其他问题

- 要想给一组一对数(x,y)排序,可以用二维int数组

  - 用一维int数组不行,因为元素是基本数据类型,lamda表达式用不了

  ![image-20220625133359389](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220625133359389.png)

- 重写hashCode方法可以直接用Objects.hash(x,y)

- Java和C++的自定义排序的思想有所不同
  - C++的cmp，满足return条件的（true），就排前边
  - Java的compare(o1,o2)函数：定义比大小的规则

```cpp
bool cmp(int a,int b){
    return a<b;	//从小到大排序
    return a>b;	//从大到小排序
}
```

​	

- `javap -c xxx.class`可以进行反编译，查看虚拟机到底做了什么
- 看到一个知乎的回答讲jvm、jre、jdk，讲的很好，将其主要观点记录下来，来自知乎用户`圆胖肿`：
  - 现在java现在已经不再强调jvm概念了
  - 以前，jvm是跨平台所需要部分的最小子集，也就是主要用来封装OS差异用的，每一种操作系统，都弄一个jvm，这样提供给上层的接口就统一了。
  - 在jvm的基础上，再加上一些常见的类库，工具，就做成了jre，也就是java的运行时runtime
  - 然后再在jre的基础之上，添加一些编译器等工具，这就是java的sdk（软件开发工具包）了，简称jdk。因此jdk是jre的超集，jre是jvm的超集，反过来，jvm是jre的子集，jre是jdk的子集。
  - 以前java提供jre和jdk两个下载，如果只是运行java的字节码，jar那些的话，只下载安装jre就行，无需安装jdk，只有开发者才需要jdk。
  - java在9的jsgsaw之后，就不再使用jvm的概念，因为jre可以被定制了，jvm和jre被拆成了一个又一个模块，可以根据需要，删减或者加入自己编写的模块。

