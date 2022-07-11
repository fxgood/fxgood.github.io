---
title: C++编程中遇到的问题
category: C++
date: 2021-03-15
excerpt: 记录C++编程中遇到的各种问题，包括语法、输入输出、STL相关的等等。
---

# 普通问题
- C++中默认的取整方式是向下取整
	- 如果需要计算`a/b`向上取整，则写成`(a+b-1)/b`即可 
	- 其含义是，将a提升一个b的倍数，但是因为减一，又到不到上面一个倍数
- C++左值、右值、左值引用、右值引用
	 - 左值和右值的概念
	 	- 左值是可以放在赋值号左边可以被赋值的值；左值必须要在内存中有实体；
         - 右值当在赋值号右边取出值赋给其他变量的值；右值可以在内存也可以在CPU寄存器。
         - 一个对象被用作右值时，使用的是它的内容(值)，被当作左值时，使用的是它的地址。
     - 引用
     	- 引用是C++语法做的优化，引用的本质还是靠指针来实现的。引用相当于变量的别名。
		- 引用可以改变指针的指向，还可以改变指针所指向的值。
		- 引用的基本规则：
			- 声明引用的时候必须初始化，且一旦绑定，不可把引用绑定到其他对象；即引用必须初始化，不能对引用重定义；
			- 对引用的一切操作，就相当于对原对象的操作。 
- bool类型数字，赋值任意非0数字，最后保存的都是1（使用的C++98是这样的）
- 复制数组`memcpy(目标数组,被拷贝数组,字节数);`
- 容器求和函数
```cpp
#include <numeric>
vector<int>arr(3,5);
cout<<accumulate(arr.begin(),arr.end(),0);	//第三个参数是sum初始值
```
- C++是静态类型语言,对象的类型在编译时必须确定,且运行时不能更改。类型转换只不过是将某种类型的对象当做另一种类型的对象来用而已,**对象本身的类型不会改变**
	- 所以试图对变量或者对象本身进行类型转换是不切实际的，如`int a; (long long)a;`或者`a=(long long)a;` 	
- C++ 伪随机数
	- 由小M多项式序列生成的，其中产生每个小序列都有一个初始值，即随机种子。（注意： 小M多项式序列的周期是65535，即每次利用一个随机种子生成的随机数的周期是65535，当你取得65535个随机数后它们又重复出现了。） 
	- 这不是真正意义上的随机数，是一个伪随机数，是根据一个数（我们可以称它为种子）为基准以某个递推公式推算出来的一系列数，当这系列数很大的时候，就符合正态公布，从而相当于产生了随机数，但这不是真正的随机数 
```cpp
#include <cstdio>
#include <cstdlib>
#include <ctime>
int main(){
    srand((unsigned)time(NULL));
    for(int i=0;i<100;i++)
        printf("%d ",rand());
    return 0;
}
```
- `next_permutation()`使用之前必须排好序！
- `lower_bound`是找数组或者容器中第一个**大于等于val**的值
- `upper_bound`是找数组或容器中第一个**大于val**的值
- **对于需要使用long long的时候，注意用int进行计算之前就要对第一个数进行强制类型转换**，如下
```cpp
LL ans=0;
for(int i=1;i<=2019;i++){
    if(check(i)){
        ans+=(LL)i*i*i;
    }
}
```
- `stringstream`重复使用一定要clear
- 定义一个引用，`Typename &A=xxx`
- 大多数编译器要求创建数组时，长度是**常量**，即直接给出长度。
	- 可以使用`new`或者`malloc`来进行动态创建 
- C++中的log函数
	- 以e为底：log(n)
	- 以10为底：log10(n)
	- 以m为底：log(n)/log(m)
- C语言<limits.h>有INT_MAX、INT_MIN，C++中推荐使用\<climits>
- 队列用push, set用insert
- CodeBlocks没有代码补全解决方法：
	- 建立工程文件
	- 修改`settings-editor-code completion`，像如下这样设置
	- 然后**重启codeblocks**
	![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210327154706509.png)

- C++中，访问迭代器指向的元素需要使用`(*it)`，若是访问对象的成员则使用`it->first`。而auto则直接使用，访问成员则使用`.`。
- C++中除了函数以外，如类、结构体，**结尾都要加`;`**
- C++11新增的emplace与之前容器所用的insert,push等的区别是，emplace自动调用构造函数先对元素进行构造再插入，而之前的是之前插入元素。
- `lower_bound()`用来寻找`[first,last)`范围内第一个**大于等于**target的元素，如果时数组，则返回该位置的指针；如果是容器，则返回该位置的迭代器。如果不存在，那么返回可以插入该元素的位置的指针或迭代器。
	- 此外用其`返回值-nums.begin()`，可以直接得到其位置。 
	- `upper_bound()`同理
- 函数指针
```cpp
int add(int a,int b){
    return a+b;
}

int sum(int a,int b,int c,int (*func)(int,int)){
    int t=func(a,b);
    return func(t,c);
}
int main(){
    int a=1,b=2,c=3;
    printf("%d",sum(a,b,c,add));
    return 0;
}
```
- 创建对象数组时，如果该对象写了构造方法则会把默认构造顶掉，需要补上。这样才能创建对象数组。
* Clion不能debug，是因为新版本问题，使用2020.2.5版本即可

* CodeBlocks不能debug是因为没有建立工程

* bool变量在java里默认是false, C+ +里没有规定,所以依赖编译器实现，千万不要让编译器决定你的默认值，永远初始化

* codeblocks下载下来编译器有问题，就去重新下自带mingw的

* gcc、g++、mingw

  * gcc 最开始的时候是 GNU C Compiler, 如你所知，就是一个c编译器。但是后来因为这个项目里边集成了更多其他不同语言的编译器，GCC就代表 the GNU Compiler Collection，所以表示一堆编译器的合集。 g++则是GCC的c++编译器。

    现在你在编译代码时调用的gcc，已经不是当初那个c语言编译器了，更确切的说他是一个驱动程序，根据代码的后缀名来判断调用c编译器还是c++编译器 (g++)。比如你的代码后缀是*.c，他会调用c编译器还有linker去链接c的library。如果你的代码后缀是cpp, 他会调用g++编译器，当然library call也是c++版本的。

    就把gcc当成c语言编译器，g++当成c++语言编译器用就是了。

  * mingw作为gcc在windows上的一个实现

* 访问迭代器所指向的对象的内部成员，括号要这样写:`(*it)->parent`

* C++中判断子串用`s1.find(s2)!=string::npos`表示s2是s1的子串

* typedef long long LL;

* 运算符重载

```cpp
struct fraction {
    int up;
    int down;

    //构造函数，并保证分子分母互质
    fraction(int a, int b) {
        int t = gcd(a, b);
        while (t != 1) {
            a /= t;
            b /= t;
            t = gcd(a, b);
        }
        up = a;
        down = b;
    }
    //求最大公因数
    int gcd(int a, int b) {
        if (b == 0)
            return a;
        return gcd(b, a % b);
    }

    //分数除法
    friend fraction operator/(fraction &f1, fraction &f2) {
        return {f1.up * f2.down, f1.down * f2.up};
    }

    //分数比大小
    friend bool operator<(fraction &f1, fraction &f2) {
        return f1.up * f2.down < f2.up * f1.down;
    }

    friend bool operator>(fraction &f1, fraction &f2) {
        return !operator<(f1, f2);
    }
    //拷贝构造函数
    fraction(fraction &b) {
        up = b.up;
        down = b.down;
    }
};
```



* C++中除了0，都是true
  * 故不要用 `return person1.age-person2.age`作为运算符重载的返回值

* 数组作为参数传递时会退化成指针，无法在函数内得出长度

* 二维数组可以使用全排列`next_permutation()`吗？
* 对于含有重复元素的序列，`next_permutation()`会**自动去重**吗？
  * 是！
  * 注意！**使用之前，序列必须已经排好序！**

* bool型占1个字节，int占4字节
  * 创建标记数组时，优先使用bool型！
  * 因为竞赛题中，对内存大小的使用是有限制的

* c/c++的编译器中有一个变量表，表的关键字段有（数据类型，变量名，首地址，值），每声明一个变量，就把这个变量的相关信息填进表中

* `next_permunation`的使用，注意需要添加`using namespace std;`和`algorithm`
	* 注意！**使用之前，序列必须已经排好序！** 

```cpp
#include <algorithm>
#include <cstdio>
using namespace std;
int main(){
    char *arr=new char[3]{'a','b','c'};
    do{
        for(char*p=arr;p<arr+3;p++)
            printf("%c ",*p);
        putchar('\n');
    }while(next_permutation(arr,arr+3));
    return 0;
}
```

* 以数组为参数的函数内部，无法正确用`sizeof(arr)/sizeof(arr[0])`计算出数组长度，因为这时的数组名arr已经退化为普通的指针，失去了原来作为数组名的特殊数据结构，所以只能在外边计算完len，作为参数传入才可。
* 使用`memset`需要添加`<cstring>`头文件，只能赋值0或者1，因为它是以字节为单位进行赋值的，如果赋其他值，可能会出错，赋其他值可以用**fill函数**，但更慢
  * `memeset(数组名，值，sizeof(数组名))`
    * **注意：这里sizeof（数组名）是计算出这个数组有多少个字节！**，直接写数组的长度会出错

* C++中的随机数

```cpp
#include <cstdlib>
#include <ctime>
srand((unsigned)time(NULL));
for(int i=0;i<10;i++){
    printf("%d ",rand());
}
```

* 其范围是`[0,RAND_MAX]`，RAND_MAX是stdlib头文件中的一个常数
  * 故如果要输出[a,b]范围内的随机数，需要使用rand()%(b-a+1)+a,
* C++全排列的写法，**注意，next_permutation第二个参数要多一位**
	* 注意！**使用之前，序列必须已经排好序！** 

```cpp
#include <iostream>
#include <algorithm>
using namespace std;
int main(){
    int arr[]={1,2,3};
    do{
        printf("%d %d %d\n",arr[0],arr[1],arr[2]);
    }while(next_permutation(arr,arr+3));		//注意这里是arr+3！！
}
```

* C++动态创建多维数组的方法

```cpp
int a,b,c,m;
scanf("%d %d %d %d",&a,&b,&c,&m);
int *ships=new int[a*b*c];
int (*attack)[7]=new int[m][7];
```

* 二维数组初始化
  * `bool mark[N][N]={false};`
* set集合添加基本数据类型以外的数据，需要添加比较器，如添加char*

```cpp
struct cmp{
    bool operator()(char*a,char*b){	//必须重载()括号运算符
        return strcmp(a,b)<0;
    }
};
set<char *,cmp> record_digits;
```

* 结构体作为vector、set、que等的参数可以直接写成这样

```cpp
#include <cstdio>
#include <queue>
using namespace std;
struct Node{
    char*s;
    int key;
    Node(char*_s,int _key):s(_s),key(_key){}
};
int main(){
    queue<Node>que;
    char*s="abc";
    que.push({s,2});
    que.push(Node(s,1));
    return 0;
}
```
* VSCode中文乱码问题
	* 因为windows默认编码是GB2312（即GBK），VSCode默认是UTF-8，所以发生中文乱码	 
	* 在设置中找到Files:Encoding,修改为Simplified Chinese(GB 2312)即可
	* 当前页面，需要点击右下角的编码进行适配
* fstream对象打开文件时，如何用自定义的文件名称
`ofs.open(date.c_str(), ios::out|ios::app);`
使用字符串的方法`c_str()`即可
* 字符数组清空
`char arr[10]={0};`
* 获取系统时间
```cpp
	struct tm t;   //tm结构指针
	time_t now;  //声明time_t类型变量
	time(&now);      //获取系统日期和时间
	localtime_s(&t, &now);   //获取当地日期和时间

	tm_sec
	分钟后的几秒 (0-59)。
	tm_min
	小时后的分钟 (0-59)。
	tm_hour
	午夜后经过的小时 (0-23)。
	tm_mday
	月 (1-31) 天。
	tm_mon
	月 (0 – 11;年 1 月 = 0）。
	tm_year
	年份 （当前年份减去 1900年）。
	tm_wday
	星期几 (0 – 6;星期日 = 0）。
	tm_yday
	每年的一天 (0-365;1 月 1 日 = 0)。
	tm_isdst
	如果夏令时有效，则为，正值夏时制不起作用; 如果为 0如果夏时制的状态是未知的负值。 如果 TZ 设置环境变量，C 运行库会假定规则适用于美国境内用于实现夏令时 (DST) 计算。
```
* switch语句中，如果要定义局部变量，需要在那句case加`{}`
* 直接在文件夹里打开codeblocks生成的exe文件，报错`找不到libgcc_s_dw2-1.dll, 尝试重新安装该程序以解决问题`

  只需要在电脑中搜索这个文件名，然后添加到系统环境变量path里`D:\Softwares\CodeBlocks\MinGW\bin`,添加其bin目录即可

* 判断int n是否素数只需判断是否整除2~&radic;n(向下取整)项

* ClodeBlocks要在General setting—Encoding settings里设置位UTF-8

* 在写1<x<2时，只能写成**1<x&&x<2**

* 使用递归计算阶乘时，要么参数传引用可以使用**--n**,要么直接传参，只能使用n-1作为下一层递归的参数

  ```cpp
  int fac(int &n)		//使用传引用  
  {
      if(n==1)
          return 1;
      return n*fac(--n);  //此时--n可以改变n的值
  }
  ```

  ```cpp
  int fac(int n)      //直接传参
  {
      if(n==1)
          return 1;
      return n*fac(n-1);		//只能用n-1作为新的参数，--n不能改变n的值
  }
  ```

* 因为0的ASCII码值为48，因此数字0~9转化为字符，只需+48即可

* 二维数组作为参数，行数可以不写，**列数要写**

* 给二维数组赋值

  ```cpp
  char CountryName[3][30];    //定义二维字符数组
      for(int i=0;i<3;i++)
          cin>>CountryName[i];
  ```

* 定义一个函数，要求该函数能够返回一个指向字符串“I love C”的指针并能在主程序中正确得到该字符串的内容

  ```cpp
  char *getString()
  {
  	char str[]="abc";	//此种写法错误！！！ 此时的str字符数组是一个局部变量
  	return str;			//当函数getString执行完毕后，str【】就被系统回收
  }
  int main()
  {
  	cout<<getString();	//无法得出
  }
  ```

  ```cpp
  //应该使用指针，则系统不会回收
  char *getString()
  {
      char *str="abc";
      return str;
  }
  ```

* int型转字符串 
	C++11标准库已添加to_string方法
* 利用指针给数组、二维数组赋值、输出，更方便，不过注意定义指向二维数组的指针时，需要如下

  ```cpp
  int *p=&maxtrix[0][0]
  ```

* 使用cin给字符串指针赋值，必须先new一个实体

  ```cpp
  string *str=new string;
  cin>>*str;		//正确
  ```

  ```cpp
  string *str;
  cin>>*str;		//错误！
  ```

* 声明函数时记得带上返回值

* 类模板的声明和实现貌似不能分开（不能放在不同文件中）

* 在实现复数与int型数相加时，重载运算符+函数为友元函数时，参数不能使用引用！

  ```cpp
  friend Complex operator+(Complex,Complex);	//使用引用会导致类型转换出错
  ```

* 写头文件的时候报错[Error] 'ostream' does not name a type

  io库的都在std中, 使用前要声明一下
  
  在头文件中使用了cout，同样要using namespace std;

# 文件操作
- 文件概念：文件是磁盘中的数据
	- ASCII文件，每个字节存放一个ASCII字符
	- 二进制文件，把内存中的存储形式原样输出到磁盘上存放
- C++中有三个用于文件操作的文件类
	 - `ifstream`，由`istream`派生来，支持从磁盘输入（输入到内存）
	 - `ofstream`，由`ostream`派生来，支持从磁盘输出  （从内存输出）
	 - `fstream`，由`iostream`派生来，支持对磁盘文件的输入输出
 - 文件操作的过程：
 	- 要以磁盘文件为对象进行输入输出，必须定义一个文件流对象，通过文件流对象将数据从内存输出到磁盘文件，或者将磁盘文件输入到内存。
 	- 定义文件流对象后，还需要将其与指定磁盘文件建立关联，并确定工作方式（输入还是输出，ASCII还是二进制）。
- 相对路径与绝对路径（windows下）
	- 相对路径
		- `./`当前文件夹下
		- `../`上一级文件夹下 
		- 这里有个坑：Clion的`exe`文件和`.cpp`文件不在同一文件夹下，所以直接使用`./xxx.txt`或者`xxx.txt`不可以 
	- 绝对路径 
		- `E:\\dir1\\dir\\a.txt` 
- 对ASCII文件的操作
	- **会自动跳过空格、回车和制表符** 
```cpp
#include <iostream>
#include <fstream>

using namespace std;

int main()
{
    ofstream outfile("a.txt", ios::out);

    if (!outfile)
    {
        cerr << "Failed to open the file!";
        return 1;
    }

    // 写入数字 1-5 到文件中
    for (int i = 1; i < 6; i++)
    {
        outfile << i << '\n';
    }

    outfile.close();

    ifstream infile("a.txt", ios::in);

    if (!infile)
    {
        cerr << "Failed to open the file!";
        return 1;
    }

    char data;  // 从文件中读出数字 1-5 
    for (int i = 1; i < 6; i++)
    {
        infile >> data;
        cout << data << '\n';
    }

    infile.close();

    return 0;
}
```
```cpp
#include <iostream>
#include <fstream>
using namespace std;
char strs[201];
int main(){
    //读取文件
    ifstream ifs("D:\\tmp.txt",ios::in);
    if(!ifs.is_open()){
        cerr<<"Fail to open file";
        return 0;
    }
    //会自动跳过空格回车换行符
    int i=0;
    while(!ifs.eof()){	//一直读到文件末尾
        ifs>>strs[i++];
    }
    for(int i=0;i<200;i++){
        cout<<strs[i];
    }
    return 0;
}
```
- 对二进制文件的操作 
```cpp
#include <iostream>
#include <fstream>

using namespace std;

int main()
{
    ofstream outfile("a.txt", ios::binary);

    if (!outfile)
    {
        cerr << "Failed to open the file!";
        return 1;
    }

    char a[] = {'h', 'e', 'l', 'l', 'o', ','};
    char b[] = {'s', 'e', 'n', 'i', 'u', 's', 'e', 'n', '!'};

    outfile.write(a, 6); // 将以 a 为首地址的 6 个字符写入文件
    outfile.write(b, 9);
    outfile.close();

    ifstream infile("a.txt", ios::binary);

    if (!infile)
    {
        cerr << "Failed to open the file!";
        return 1;
    }

    char data[6];
    infile.read(data, 6);  // 从文件中读出 6 个字符到以 data 为首地址的字符数组中
    for (int i = 0; i < 6; i++)
    {
        cout << data[i];
    }

    char datb[6];
    infile.read(datb, 9);
    for (int i = 0; i < 9; i++)
    {
        cout << datb[i];
    }

    infile.close();

    return 0;
}
```
# 输入输出问题

- printf对浮点数的摄入规则**四舍六入五成双——**`  四舍六入五考虑，五后非零就进一，五后为零看奇偶，五前为偶应舍去，五前为奇要进一`
  - 那么如何手动实现四舍五入的效果呢？比如要实现四舍五入保留两位小数，则`double a=1.0050;printf("%.2f",(a*100+0.5)/100)`

- 输入一行数据两种方法的比较：`gets(char*)`,`scanf("%s",&arr)`
	-  `gets(char*)`：
		- 遇到buff中的第一个`\n`立马结束输入，然后将字符数组的字符串结尾处后一个设置为`\0`
		- 如果控制台只输入一个`\n`（buff中第一个就是回车），那么gets会立即结束输入，并且将字符数组第一个位置写成`\0`	
	-  `scanf("%s",&arr)`
		-  会将两个enter之间的内容放入arr字符数组中，并且结尾后一位会放上`\0`
		- 如果buff中`\n\n\nabc\n`，则会吸收掉前面三个回车和后边一个回车，将abc写入字符数组
- 输入一行数据（字符串）的两种方法
	- 第一种：getline(cin,str)
	- 第二种：cin.getline(char []buff,100) 
```cpp
string temp;
getline(cin,temp);
```
```cpp
char buff[100];
cin.getline(buff,100);
int len=strlen(buff);
string temp2(buff,len);
```
> 注意在使用之前，如果有cin，或者scanf（除%c外），则需要吸收回车
- `cin`和`scanf`的区别
	- scanf使用的是输入缓冲，cin使用的是输入流，但是输入流会主动与输入缓冲同步
		- `std::ios::sync_with_stdio(false);`输入流可以设置设置不与输入缓冲同步
	- `scanf("%c")` 
		- %c是一个很奇葩的设定，单独读入一个字符，包括不可见的控制字符（换行等），而其他格式化符号（如%d %lld %f %lf %s等）会在读入未完成时将换行符、空格、制表符等空白字符统统舍弃忽略（**但是会留在输入缓冲中**），直到读到了足够的数据或遇到文件结尾才结束。 
	- 换行符（等其他不可见控制字符）问题
		- cin（cin所有类型，包括cin>>char类型变量)，和，scanf除%c以外的scanf的情况，遇到控制字符（如换行符）在读取时会直接跳过（不读取），**但是输入的最后一个控制字符（如换行符），会留在输入缓冲中。**因此如果后边紧跟着scanf的%c，一定要用getchar来吸收掉控制字符。
	- %c和其他（如cin，%f）等连用的问题
		- 如上边所说，如果前边有除scanf %c以外的所有输入情况，都得吸收控制字符。
	- **以上讨论暂不包括行输入**。 
		- 两种输入一行字符串的方法`getline(cin,str)`和`cin.get(char arr[],100)`，完成输入之后，**不会在输入缓冲区留下控回车**
- 输入一行数据，可以使用`getline(cin,str)`
	- 关于换行符的问题：因为`scanf()`会将换行符留在缓存中，所以如果**geline前边有scanf，则需要`getchar()`吸收回车，循环getline之间并不需要吸收换行符。**  
- 对字符进行输入时，一行结束，要吸收空格
- 把时间中的`3点12分`输出成`03:12`
	- `printf("%02:%02d" ,h,m) 	`
- 读入一行数据可以使用(注意读入一行数据前如果有单个数据读入，需要吸收换行符）
	- 将一行数据中的某些信息（如字符串中的数字）进行提取，可以使用`sscanf`; 
```cpp
int N;
cin>>N;
cin.get();	//吸收换行符

char line[100];
cin.getline(line,100)
int len=strlen(line);

sscanf(line,"%d:%d:%d (+%d)",&h,&m,&s,&d);
```

- `printf`输出浮点数采取的保留小数措施是**四舍六入五成双**，比如保留2位小数时
	- 第二位小数，如果<=4则舍去，如果>=6则进位
	- 如果==5
		- 5后边还有数，那么舍5进1
		- 5后没有数
			- 5前是奇数，则舍5进1
			- 5前是偶数，则舍去5    

* `%5s`表示输出宽度为5的字符串（如果长度不够，左边补空字符）
  * `printf("%*s",5,"abc");`有上述同样的效果

* scanf中
  * float是`%f`，double是`%lf`
  * long long是`%lld`
* printf中
  * float和double都是`%f`
  * long long仍然是`%lld`

# 字符串相关问题

- c++分割字符串的方法stringstream和getline；stringstream，需要.str仅仅clear并没有释放内存，会导致内存使用超出

- 根据分隔符分割字符串
```cpp
//将一个字符串，根据分隔符来切分
vector<string> splitStr(string s,char seq){
    vector<string>ans;
    string tmp;
    for(auto&ch:s){
        if(ch!=seq)
            tmp.push_back(ch);
        else{
            ans.push_back(tmp);
            tmp.clear();
        }
    }
    ans.push_back(tmp);
    return ans;
}
```
- C++字符转string类型的方法是，将字符放在字符数组中，使用string的构造函数来实现。
	- `string str(char *arr, 字符个数)` 
- string的find返回的是找到的子串的开头index，如果没找到返回-1
- 在父串中查找存在多少子串
```cpp
#include<iostream>
#include<string>
#include <set>
using namespace std;
set<int>first_pos;
int hasHowManySub(string str1,int left,int right,string str2){
    if(left>right)
        return 0;
    int pos=str1.find(str2,left);
    if(pos==-1){
        return 0;
    }
    first_pos.insert(pos);
    return 1+hasHowManySub(str1,left+1,right,str2);
}
int main(){
    string str1="2020203332020";
    string str2="2020";
    int ans=hasHowManySub(str1,0,str1.length()-1,str2);
    cout<<ans<<endl;
    for(set<int>::iterator it=first_pos.begin();it!=first_pos.end();it++){
        cout<<*(it);
    }
}

```
- `print("%s")`无法打印string类型的数据，因为`string`是STL容器，前边的这种写法只能打印字符数组（结尾是`\0`）
- C++取子串的两种方式
	- `string str2(str1, 起始下标，截取连续多少个字符）`
	- `str1.substr(起始下标，截取连续多少个字符）` 

* 可以用字符数组生成一个string对象
  * `char words[1000001];`
  * `gets(words);`
  * `string text(words);`

* C++中字符串`==`是比较字典序是否相同

* 数字转字符串使用`<sstream>`
  * **注意，在进行多次类型转换前，必须先运行clear()**
* 可以使用`<algorithm>`中的sort来对字符串进行排序

* 逆置字符串:`reverse(s.begin(),s.end());`

* 手动拷贝字符数组（字符串）时，要在末尾加`\0`

* 关于字符和字符串的输入，某些情况下，要注意用getchar()吸收回车

```cpp
char a,b;
scanf("%c",&a);
scanf("%c",&b);
printf("a=%c,b=%c",a,b);
//控制台如下
a 	//输入a然后enter，没等输入b直接出了结果，把回车（换行符）赋给了b
a=a,b=
```



* 数字转字符串

```cpp
#include <iostream>
#include <sstream>
using namespace std;
void intToStr(int x,string&res){
    stringstream ss;
    ss<<x;
    ss>>res;
}
int main(){
    string s;
    intToStr(123,s);
    cout<<s<<endl;
}
```

* 字符串查找子串

```cpp
string a="abc";
if(a.find("d")==string::npos)
    cout<<"There is no 'd'"<<endl;
//如果找到了
```

* sscanf的用法（在C标准库中,\<cstdio>或者<stdio.h>）

```cpp
char ss[]="10:12:32";
int h,m,s;
sscanf(ss,"%d:%d:%d",&h,&m,&s);
printf("%d,%d,%d",h,m,s);
```

* 结构体作为参数时的快捷写法

```cpp
struct Point{
    int x;
    int y;
};
//
void f(int i,int j){
	queue<Point>que;
	que.push({i,j});    
}

```

* 应该是编译器做了优化，对两个内容相同的字符串指针进行比较，结果是true

```cpp
int main(){
    char*a="abc";
    char*b="abc";

    int c=1,d=1;
    int *cc=&c;
    int *dd=&d;

    set<int*>s1;
    set<char*>s2;

    s2.insert(a);
    s2.insert(b);

    s1.insert(cc);
    s1.insert(dd);

    printf("%d\n", s1.size());
    printf("%d",s2.size());
}

//结果是
2
1
```

* 对一个字符指针来说
  * 使用`%s`则输出字符串
  * 使用`%p`则输出字符指针地址

# 迭代器
# STL相关问题

- 比较STL容器内容是否完全相同，可以用`==`（内部进行了运算符重载）
- 在一个函数内部定义一个容器，然后返回他，函数结束之后返回的容器会被销毁吗？
  - 答：不会！因为返回时会调用拷贝构造函数，函数中的局部变量容器确实可能会被销毁。如果返回的是引用或者地址，则可能会被销毁！因为没有调用拷贝构造函数。

## map
- 比较两个map内容是否完全相同，可以用`==`
**注意**：map1中`key1:0`，map2中没有key1，则判断结果为false
- 访问不存在的索引时，会自动插入该索引key，并且将value初始化（数字则初始化为0，字符串则初始化为空字符串）
	- 因此在某些场景下，如统计字符串中每个字符的个数，可以直接map[xx]++；但是在其他场景下，会导致额外的key被插入map，应当要先判断key是否存在。 
## vector
- set转vector
	- `vector<int>vec(s.begin(), s.end() )`; 
- 复制vector只需要`vector t1,t2;  t2=t1;`
- C++98 vector初始化方法
	- `vector<int> ilist1` 
	- `vector<int> ilist2(ilist)` 或者 `vector<int> ilist2  = ilist`
	- `vector<int> ilist3(ilist.begin()+2,ilist.end()-1)`
	- `vector<int> ilist4(7)` 或者 `vector<int> ilist5(7,3)`指定长度为7，每个元素都为3
- vector的初始化
	- `vector<int> ilist1` 
	- `vector<int> ilist2(ilist)` 或者 `vector<int> ilist2  = ilist`
	- `vector<int> ilist = {1,2,3.0,4,5,6,7}`或者`vector<int> ilist {1,2,3.0,4,5,6,7}`
	- `vector<int> ilist3(ilist.begin()+2,ilist.end()-1)`
	- `vector<int> ilist4(7)` 或者 `vector<int> ilist5(7,3)`指定长度为7，每个元素都为3
- 指定位置插入数据
```cpp
v2.insert(v2.begin()+4, L"3");   //在指定位置，例如在第五个元素前插入一个元素
 
v2.insert(v2.end(), L"3");   //在末尾插入一个元素
 
v2.push_back(L"9");   //在末尾插入一个元素
 
v2.insert(v2.begin(), L"3");   //在开头插入一个元素
```
* 插入数据使用`push_back`

除了`vector<bool>`以外，都可以用`for(auto&i:vector)`进行遍历，如果是bool，不能加引用`&`
## pair
- 需要添加头文件`<utility>`和`using namespace std;`；
	- 或者添加`<map>`，因为map中会自动添加`<utility>` 
- pair作为set或map集合的元素时，其唯一性判断是根据pair的第一个参数来的，与第二个参数无关
* 创建pair的方法
  * `make_pair("haha",2)`;
  * `pair<string,int>("haha",2)`
* pair中已经定义好了比大小、判断相等的函数
  * 因此`pair<type T1,Type T2>`中如果是基本数据类型，则可以直接进行比大小
  * 规则是：先比第一个，再比第二个

## set
- set合并：用insert即可
* set集合去重的原理
	* **使operator<函数，在a<b时返回true，在a==b或者a>b时，都返回false** 	
  * **尽量不要自己自定义去重逻辑，会出现很大问题**
    
  * 其实，set容器在判定已有元素a和新插入元素b是否相等时，是这么做的：1）将a作为左操作数，b作为有操作数，调用比较函数，并返回比较值 2）将b作为左操作数，a作为有操作数，再调用一次比较函数，并返回比较值。如果1、2两步的返回值都是false，则认为a、b是相等的，则b不会被插入set容器中；如果1、2两步的返回值都是true，则可能发生未知行为，**因此，记住一个准则：永远让比较函数对相同元素返回false。**
  
  * 其实，set容器在判定已有元素a和新插入元素b是否相等时，是这么做的：
    1）将a作为左操作数，b作为有操作数，调用比较函数，并返回比较值
    2）将b作为左操作数，a作为有操作数，再调用一次比较函数，并返回比较值。
  
    如果1、2两步的返回值都是false，则认为a、b是相等的，则b不会被插入set容器中
  
    **如果1、2两步的返回值都是true，则可能发生未知行为**
  
  * 重载<运算符，和自定义struct比较器，都是一样的逻辑
  
  * 为甚么要重载<运算符呢？能不能重载"<="或者">="运算符？答案是不可以。几乎所有的方法或容器都需要排序来满足数学意义上的标准严格弱序化，否则这些方法或容器的行为将不可预知。假设f(x,y)是一个比较函数。 如果该函数满足如下条件则它是严格弱序化的。
  

**这是一个成功的案例**
```cpp
struct Node {
    char outer[13];
    char mid[9];
    char inner[5];

    Node(string a, string b, string c) {
        for (int i = 0; i < 12; i++)
            outer[i] = a[i];
        for (int i = 0; i < 8; i++)
            mid[i] = b[i];
        for (int i = 0; i < 4; i++)
            inner[i] = c[i];
        outer[12]='\0';
        mid[8]='\0';
        inner[4]='\0';
    }
    //逻辑：不满足a<b，的统统返回false
    bool operator<(const Node &a) const {
        if (strcmp(outer, a.outer) < 0)
            return true;
        if (strcmp(outer, a.outer) > 0)
            return false;
        //如果等于，得看下一层
        if (strcmp(mid, a.mid) < 0)
            return true;
        if (strcmp(mid, a.mid) > 0)
            return false;
        //还等于，还得看下一层
        return strcmp(inner, a.inner) < 0; //除a<b,其他情况都返回false
    }
};
```
* set集合添加基本数据类型以外的数据，有两种方式
  * 添加比较器
  * 在类（结构体）中进行`<`运算符重载

```cpp
struct cmp{
    bool operator()(char*a,char*b){	//必须重载()括号运算符
        return strcmp(a,b)<0;
    }
};
set<char *,cmp> record_digits;		
```

## priority_queue
- **注意优先级队列的比较跟set他们是反着的**
- `priority_queue`对比大小的三种定义方式
	- 定义一个结构体类型的比较方法
	- 友元函数
	-  在自定义类型中定义比较函数

```cpp
struct cmp
{
	//作用是，让第二个参数小的优先级高
    bool operator()(pair<int,int>&p1,pair<int,int>&p2)
    {
        return p1.second>p2.second;
    }
};

 priority_queue<pair<int,int>,vector<pair<int,int> >,cmp> pq;
```
```cpp
struct person{
    int age;
    friend bool operator<(person p1,person p2){
        return p1.age<p2.age;   //表示age大的优先级高
    }
};
```
```cpp
struct Node{
    int time;
    int id;
    Node(int t,int i):time(t),id(i){}
    bool operator<(const Node &n)const{
        return time<n.time;
    }
};
```

- priority_queue使用的例子
	- 题目：给定一个非空的整数数组，返回其中出现频率前 k 高的元素。 
	- **注意，priority_queue的cmp比较，与`sort`中是相反的**
```cpp
struct cmp
{
	//作用是，让第二个参数小的优先级高
    bool operator()(pair<int,int>&p1,pair<int,int>&p2)
    {
        return p1.second>p2.second;
    }
};

class Solution
{
public:
    vector<int> topKFrequent(vector<int>& nums, int k)
    {
        map<int,int>mp;
        for(int i=0; i<nums.size(); i++)
        {
            mp[nums[i]]++;
        }
        //优先级队列，频次低的优先级高
        priority_queue<pair<int,int>,vector<pair<int,int> >,cmp> pq;
        for(map<int,int>::iterator it=mp.begin(); it!=mp.end(); it++)
        {
            if(pq.size()<k)
            {
                pq.push({it->first,it->second});
            }
            else{
                if(pq.top().second < it->second){
                    pq.pop();
                    pq.push({it->first,it->second});
                }
            }
        }
        vector<int>ans;
        while(!pq.empty()){
            ans.push_back(pq.top().first);
            pq.pop();
        }
        return ans;
    }
};
```
- priority_queue默认是一个大根堆
	- `priority_queue<int,vector<int>,less<int> >q`：第二个参数vector\<int>表示用来承载底层数据结构堆(heap)的容器，less\<int>**表示数字大的优先级越大**，而greater\<int>表示数字越小的优先级越大。 

* `priority_queue`的使用

```cpp
#include <cstdio>
#include <queue>
using namespace std;
struct person{
    int age;
    friend bool operator<(person p1,person p2){
        return p1.age<p2.age;   //表示age大的优先级高
    }
};
int main() {
    priority_queue<person>priorityQueue;
    priorityQueue.push(person{1});
    priorityQueue.push(person{3});
    priorityQueue.push(person{2});
    priorityQueue.push(person{5});
    while(!priorityQueue.empty()){
        person front=priorityQueue.top();
        printf("%d\n",front.age);
        priorityQueue.pop();
    }
    return 0;
}
//打印结果
5
3
2
1
```
## deque
双端队列的头文件`<deque>`
具体用法见：[deque用法](https://www.cnblogs.com/elvisHuster/p/12584537.html)
## others

* 使用`pair`需要添加头文件`utility`和using namespace std;

* 嵌套vector<\<vector> >，中间要加括号，否则有些C++11之前的编译器，会把它当成移位操作
* STL中，**只有在vector和string中，才允许使用vi.begin()+3**这种迭代器加上整数的写法

* `sort`函数需要加上`<algorithm>`和`using namespace std;`

  * sort(首元素**地址**，尾元素的**下一个地址**,比较函数)

  ```cpp
  bool cmp(int a,int b){
      return a>b;
  }	//从高到低排列
  ```

  * 第三个参数填入cmp，即可实现从大到小排序，**注意**，如果在类里边定义cmp函数，需要加static，如下：
```cpp
class Solution {
public:
    static bool cmp(int a,int b){
        return a>b;
    }
    int findKthLargest(vector<int>& nums, int k) {
        sort(nums.begin(),nums.end(),cmp);
        return nums[k-1];
    }
};
```

* 队列出队，先用front再用pop

```cpp
Point head=que.front();
que.pop();
```

* 动态二维数组

```cpp
vector<vector<int> >group;
//指定外层初始长度
vector<vector<int> >group(K);
//指定外层和内层初始长度
vector<vector<int> >group(K,vector<int>(3));
```
# lambda表达式
## 1、概念
lambda表达式是C++11中引入的，它是一种匿名函数，通常它作为一个参数传递给接收函数指针或者函数符的函数使用。在C++的STL算法使用过程中，经常可以看到它的身影。 
## 2、优点
1. 遵守就近原则：随时定义随时使用，lambda表达式的定义和使用在同一个地方，并且lambda表达式可以直接在其他函数中定义使用，其他函数没有这个优势。
2. 简洁明了：lambda表达式相比较其他函数的更加的简洁明了。
3. 效率相对高些：lambda表达式不会阻止编译器的内联，而函数指针则会阻止编译器内联。
4. 捕获动态变量：lambda表达式可以捕获它可以访问的作用域内的任何动态变量。
## 3、基本语法
[capture list] (params list) mutable exception-> return type { function body }
表达式中各个参数的含义如下：
[capture list]:捕获外部变量列表
(params list):形参列表
mutable:表示能不能修改捕获的变量
exception:异常设定
return type:返回类型
function body:函数体
虽然lambda中的参数变量很多，通常情况下并不需要把每一个都使用上，根据自己的需要使用即可，大多数情况下可以直接省略->、mutable、exception，不需要返回类型的话也可以省略return type。一个简单的lambda表达式：[](int x){return x*x;}。
## 4、捕获外部变量的办法
1. 参数以值传递方式被捕获：参数以值传递的方式传入到lambda表达式中，值传递方式参数在lambda表达式中值不能被修改。
2. 参数引用传递方式被捕获：参数以引用的方式传入到lambda表达式中，引用传递方式参数在lambda表达式内部修改和表达式外部的修改会相互影响。
3. 表达式自行推导捕获：当捕获列表[capture list]中不传入任何变量而是写成[=]或者[&]，lambda表达式会以值传递[=]或[&]引用传递的方式捕获参数，参数的捕获根据表达式中变量使用情况来推导。

# 语法问题
- 两个int相乘或者相加，要注意溢出的问题

**正确写法**：先强转再相乘
```cpp
int a=xxx;
long long b=(long long )a*a;
```
**错误写法**：先相乘再强转，已经丢失精度了
```cpp
int a=xxx;
long long b=(long long )(a*a);
```
# 蓝桥杯注意事项（ISO98标准）
- 非静态成员不能初始化
- 嵌套容器的`> >`要空一格 
- `vector<pair<int,int> >`不能直接vec.push_back({1,2});这是C++11的语法
	- 需要写成`vec.push_bakc(make_pair(1,2));` 
- 二维vector初始化`vector<vector<bool> >mark(A.size(),vector<bool>(A[0].size(),true));`
- 数组无法直接转换成vector，需要使用for循环一个一个添加
- 有double的题目，一定要小心，不要把double写成int了！
- 存在long long数据的时候，写函数一定要注意相应的函数参数**不能写成int**；
- 读入一行最好的方法是`sacnf("%s",字符数组名)`
	- 因为其会舍弃前后的enter，只将中间的内容写入字符数组 