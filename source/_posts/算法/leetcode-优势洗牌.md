---
title: leetcode 优势洗牌
date: 2021-07-30
category: 算法
tag:
- 贪心
- leetcode
---

今天遇到一道算法题[870优势洗牌](https://leetcode-cn.com/problems/advantage-shuffle/)，发现这个问题类似著名的田忌赛马问题。

使用贪心算法可以有效解决这个问题，其思想核心是：比得过就比，比不过就找个最菜的垫背。

下面的C++代码也值得品味，使用了Lambda表达式。

<!--more-->

```cpp
class Solution {
public:
    vector<int> advantageCount(vector<int> &nums1, vector<int> &nums2) {
        int len = nums1.size();
        vector<int> ans(len);    //结果集
        /*记录nums2的数字和下标*/
        vector<pair<int, int>> nums3;
        nums3.reserve(len); //预留vector空间
        for (int i = 0; i < len; i++)
            nums3.emplace_back(nums2[i], i);
        /*对nums1和nums3进行由大到小排序*/
        sort(nums3.begin(),nums3.end(),[](pair<int,int>&a,pair<int,int>&b){
            return a.first>b.first;
        });
        sort(nums1.rbegin(),nums1.rend());  //逆序排列
        /*双指针进行比较*/
        int left=0,right=len-1;
        for(auto &item:nums3){
            if(item.first<nums1[left]){
                ans[item.second]=nums1[left];
                left++;
            }else{
                ans[item.second]=nums1[right];
                right--;
            }
        }
        return ans;

    }
};
```
**注意**：这里的vector.reserve（储备），是在已知长度的情况下，为vector预留空间，这样后序添加的时候，就无需频繁扩容（含有数组的赋值），提高效率。