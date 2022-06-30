---
title: leetcode 求1+2+…+n
category: 算法
tag:
- leetcode
---

[leetcode](https://leetcode-cn.com/problems/qiu-12n-lcof/)

前置知识：快速乘(俄罗斯农民乘法）（

前提：a和b是正整数

```java
int quickMuilti(int a,int b){
    int ans=0;
    while(b>0){
        if((b&1)>0)
            ans+=a;
        a<<=1;
        b>>=1;
    }
    return ans;
}
```



原理：

A\*B=A\*(2^0+2^3+2^10+..)=((A<<1)+(A<<3)+(A<<10)+...)



回到本题，其中if不能用，则可以用逻辑运算的短路效应代替，for循环不能用，则n的最大值是10000，因此手动写14次循环即可。

```java
class Solution {
    //sum=n*(1+n)/2
    //A*B=A*(2^0+2^3+2^10+..)=((A<<1)+(A<<3)+(A<<10)+...)
    //与2的幂相乘，等价于左移
    public int sumNums(int n) {
        int ans=0;
        int a=n;
        int b=(n+1);
        boolean s;
        s=(b&1)>0 && (ans+=a)>0;
        a<<=1;
        b>>=1;
        s=(b&1)>0 && (ans+=a)>0;
        a<<=1;
        b>>=1;
        s=(b&1)>0 && (ans+=a)>0;
        a<<=1;
        b>>=1;
        s=(b&1)>0 && (ans+=a)>0;
        a<<=1;
        b>>=1;
        s=(b&1)>0 && (ans+=a)>0;
        a<<=1;
        b>>=1;
        s=(b&1)>0 && (ans+=a)>0;
        a<<=1;
        b>>=1;
        s=(b&1)>0 && (ans+=a)>0;
        a<<=1;
        b>>=1;
        s=(b&1)>0 && (ans+=a)>0;
        a<<=1;
        b>>=1;
        s=(b&1)>0 && (ans+=a)>0;
        a<<=1;
        b>>=1;
        s=(b&1)>0 && (ans+=a)>0;
        a<<=1;
        b>>=1;
        s=(b&1)>0 && (ans+=a)>0;
        a<<=1;
        b>>=1;
        s=(b&1)>0 && (ans+=a)>0;
        a<<=1;
        b>>=1;
        s=(b&1)>0 && (ans+=a)>0;
        a<<=1;
        b>>=1;
        s=(b&1)>0 && (ans+=a)>0;
        a<<=1;
        b>>=1;
        s=(b&1)>0 && (ans+=a)>0;
        ans>>=1;
        return ans;
    }
}
```

