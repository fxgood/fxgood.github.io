---
title: 最长递增子序列的长度
date: 2021-08-13
category: 算法
tag:
- leetcode
- 贪心
- 二分
---

[300最长递增子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

这一题如果使用动态规划来写，那么会到达$O(N^2)$的时间复杂度。由于不需要求出具体的最长递增子序列，只需要求出最长的长度，那么可以使用**贪心+二分**，将时间复杂度降低到$O(N*logN)$

**思路**：维护一个升序队列，在遍历nums的过程中，不断优化升序队列的结构（使每个元素尽可能小），这样给后序元素的添加提供了更大的数字空间。

<!--more-->

```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
//维护一个升序队列，在遍历nums的过程中，不断优化升序队列的结构（使每个元素尽可能小），这样给后序元素的添加提供了更大的数字空间。
        vector<int>arr;
        for(auto&n:nums){
            auto it=lower_bound(arr.begin(),arr.end(),n);
            //所有元素都小于n（或为空）
            if(it==arr.end()){
                arr.push_back(n);
                continue;
            }
            //替换第一个大于等于它的元素，以优化结构（瘦身），使得后序元素有更大的数字空间来进栈
            *it=n;
        }
        return arr.size();
    }
};
```
> 注意这里使用了lower_bound()，底层是对顺序表的二分查找，时间复杂度是O(logN)