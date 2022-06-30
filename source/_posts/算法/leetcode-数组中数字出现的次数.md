---
title: leetcode-数组中数字出现的次数
category: 算法
tag:
- leetcode
---

[leetcode](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-lcof/)

精髓在于：分组异或操作。将所有数字异或之后，以二进制某一位为1的位置来作为分组的依据（任取全部异或后结果为1的位置即可）。

凡是在这一位置上为0的分到一组，其余分到另一组。这样能够保证相同数字肯定分到一组（相同数字每一位上的1/0都相同），同时两个出现一次的数字肯定不在一组。

其中，lowbit操作可以找到一个数字为1的一位。

```java
class Solution {    
    int lowbit(int x){
        return x&(-x);
    }

    //贪心 dp 二分 排序 搜索 位运算
    public int[] singleNumbers(int[] nums) {
        int res=0;
        for(int i:nums)
            res^=i;
        int r1=0,r2=0;
        int t=lowbit(res);
        for(int i:nums){
            if((t&i)==0)
                r1^=i;
            else
                r2^=i;
        }
        return new int[]{r1,r2};
    }
}
```

