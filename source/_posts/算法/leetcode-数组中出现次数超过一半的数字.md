---
title: leetcode-数组中出现次数超过一半的数字
category: 算法
tag:
- leetcode
---
[leetcode](https://leetcode-cn.com/problems/shu-zu-zhong-chu-xian-ci-shu-chao-guo-yi-ban-de-shu-zi-lcof/)

方法1：先排序，然后取最中间的数字

方法2：摩尔投票。其思路是：每次去掉两个不同的数字，直到不能删除为止，剩下的就是众数。（如果删掉的两个数字都不是众数，那么对结果没有影响；如果删掉的其中一个是众数，那么对结果也没有影响）

```java
class Solution {
    public int majorityElement(int[] nums) {
        int candidate=0;
        int cnt=0;
        for(int i=0;i<nums.length;i++){
            if(cnt==0){
                cnt=1;
                candidate=nums[i];
            }else{
                if(nums[i]==candidate)
                    cnt++;
                else
                    cnt--;
            }
        }
        return candidate;
    }
}
```

