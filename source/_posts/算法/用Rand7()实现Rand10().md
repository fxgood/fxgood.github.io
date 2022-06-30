---
title: 用Rand7()实现Rand10()
category: 算法
---

[leetcode470](https://leetcode.cn/problems/implement-rand10-using-rand7/)

这里做法非常巧妙,先奇偶性凑出1/2概率,然后用等概率的1~5凑出等概率的1~10

<!--more-->

```java
/**
 * The rand7() API is already defined in the parent class SolBase.
 * public int rand7();
 * @return a random integer in the range 1 to 7
 */
class Solution extends SolBase {
    public int rand10() {
        int t1,t2;
        while((t1=rand7())==7);
        while((t2=rand7())>5);
        return (t1&1)==0?t2+5:t2;
    }
}
```

