---
title: leetcode 分割数组的最大值
date: 2021-08-02
category: 算法
tag:
- 二分
- leetcode
---

[410分割数组的最大值](https://leetcode-cn.com/problems/split-array-largest-sum/)

**遇到诸如 “xx最大值的最小”，这类问题，一般都可以采用二分来解决。**

这一题正向思维不好解决，需要**逆向思维**，直接对**分组和的最大值**进行二分搜索，其下限是数组中最大元素，上限是所有元素的和。枚举分组和的最大值后，逆向判断在此情况下，能否使得数组分为m个分组（贪心解决）。

<!--more-->

```cpp
class Solution {
public:
    bool check(vector<int>& nums, int x, int m) {
        long long sum = 0;
        int cnt = 1;
        for (int i = 0; i < nums.size(); i++) {
            if (sum + nums[i] > x) {
                cnt++;
                sum = nums[i];
            } else {
                sum += nums[i];
            }
        }
        return cnt <= m;
    }

    int splitArray(vector<int>& nums, int m) {
        long long left = 0, right = 0;
        for (int i = 0; i < nums.size(); i++) {
            right += nums[i];
            if (left < nums[i]) {
                left = nums[i];
            }
        }
        while (left < right) {
            long long mid = (left + right) >> 1;
            if (check(nums, mid, m)) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }
};
```