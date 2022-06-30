---
title: 搜索旋转排序数组II
date: 2021-03-21
category: 算法
tag:
- 二分
---

> 刷到leetcode81题搜索旋转排序数组觉得有点意思，遂记录下解题思路和过程。要求实现O(logn)的时间复杂度。

**要抓住二分查找的核心，其在于每次排除掉一边的区间，故不在乎整个数组是否是完全递增的，只要每次能排除掉一边的区间，选择另一个区间继续进行二分查找即可**。

<!--more-->

<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210321094534293.png" height=50% width=50%><br>
对于这一题，数组的大小顺序形如下图：
<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210321101635443.png" width=30% height=30%><br>
我们以**右端点为参照**对象进行分类，当`中间值==target`时返回true，否则：

- 如果mid值小于右端点时：
<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210321102133993.png" width=30% height=30%><br>
如果`中间值<target<=右端点`，则下一步对mid右侧进行二分查找，否则对mid左侧进行二分查找
- 如果mid值大于右端点时：
<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210321102505188.png" width=30% height=30%><br>
如果`左端点<=target<=中间值`，则下一步对mid左侧进行二分查找，否则对mid右侧进行二分查找
- 如果mid值等于右端点时：
**直接让右端点向左挪一个单位**。（因为target不等于mid值，mid值又等于右端点的值，因此右端点可以被抛弃）

代码如下：

```cpp
    bool search(vector<int>& nums, int target) {
        int left=0,right=nums.size()-1;
        while(left<=right){
            int mid=(left+right)/2;
            //命中则返回true
            if(nums[mid]==target)
                return true;
            //以右端点为参照进行分类
            if(nums[mid]<nums[right]){
                //就两种情况，要不在左半边，要不在右半边
               if(target>nums[mid] && target<=nums[right])
                   left=mid+1;
               else
                   right=mid-1;
            }else if(nums[mid]>nums[right]){
            	//就两种情况，要不在左半边，要不在右半边
                if(target>=nums[left] && target<nums[mid])
                    right=mid-1;
                else
                    left=mid+1;
            }else{
             //抛弃右端点
             right--;
            }
        }
        return false;
    }
```

---
这里再看一道旋转数组的变形，同样要求时间复杂度O（logn）：
<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210321105730486.png" height=50% width=50%><br>
这里同样应用刚才说的二分查找的核心思路，每次排除掉一边，对另一边继续进行二分查找。代码如下：

```cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        if(nums.size()==1)
            return nums[0];
        int left=0,right=nums.size()-1;
        while(left<right-1){	//缩小到最小区间
            int mid=(left+right)/2;
            if(nums[mid]<nums[right]){
            	//最小值只有可能在左侧
                right=mid;
            }else if(nums[mid]>nums[right]){
            	//最小值只可能在右边
                left=mid;
            }else
            	//右端点此时不可能是最小值，排除
                right--;
        }
        return min(nums[left],nums[right]);	//返回最后两个候选人中较小的
    }
};
```