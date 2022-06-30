---
title: 数组中的逆序对
category: 算法
tag:
- leetcode
---

[leetcode](https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)

核心思想：归并排序与逆序对有着天然的练习。两个有序数组num1和num2进行merge时，使用双指针p1,p2; 如果num1[p1]>num2[p2]时，那么num2[p2]与num1[p1]，num1[p1+1]...num1[num1.size()-1]，即p1所指元素后的所有元素都会形成逆序对。

那么在归并排序的过程中统计这个数量即可。

```java
class Solution {
    public int reversePairs(int[] nums) {
        mergeSort(nums,0,nums.length-1);
        return cnt;
    }

    void mergeSort(int[]nums,int l,int r){
        if(l>=r)
            return;
        int mid=l+(r-l)/2;
        mergeSort(nums,l,mid);
        mergeSort(nums,mid+1,r);
        merge(nums,l,mid,r);
    }
    int cnt=0;
    void merge(int[]nums,int l,int mid,int r){
        int p1=l,p2=mid+1;
        int n=r-l+1;
        int[]tmp=new int[n];
        int t=0;
        while(p1<=mid && p2<=r){
            if(nums[p2]<nums[p1]){  //统计逆序对个数
                cnt+=mid-p1+1;
                tmp[t++]=nums[p2++];
            }
            else{
                tmp[t++]=nums[p1++];
            }
        }
        while(p1<=mid)
            tmp[t++]=nums[p1++];
        while(p2<=r)
            tmp[t++]=nums[p2++];
        for(int i=0;i<n;i++)
            nums[l+i]=tmp[i];
    }
}

```

