---
title: leetcode215-数组中的第K个最大元素
category: 算法
tag:
- leetcode
---

可以基于快速排序或者堆排序来写。

<!--more-->

# 方法一：基于快速排序

快速排序中，每一轮能够确定一个元素的最终位置。其本质是**分治算法**。

这里划分时随机选取pivot有个小技巧，就是随机选下标后跟第一个元素进行交换，这样后续就能符合常见的快速排序的写法了。

```java

public class Solution {
    private void swap(int[]arr,int a,int b){
        int tmp=arr[a];
        arr[a]=arr[b];
        arr[b]=tmp;
    }
    public int findKthLargest(int[] nums, int k) {
        //目标元素下标应该是排好序后的target位置
        int target=nums.length-k;
        int left=0,right=nums.length-1;
        while(true){
            //将所有小于nums[t]的放左边，所有大于nums[t]的放右边
            int l=left,r=right;
            //随机选基准元素
            int pivot=new Random().nextInt(r-l+1)+l;
            swap(nums,l,pivot);
            int value=nums[l];
            while(l<r){
                //从右边找小于value的
                while(l<r && nums[r]>=value) r--;
                swap(nums,l,r);
                //从左边找大于value的
                while(l<r && nums[l]<=value) l++;
                swap(nums,l,r);
            }
            nums[l]=value;
            if(l==target)
                return value;
            else if(l<target){
                left=l+1;
            }
            else{
                right=l-1;
            }
        }

    }
}

```



# 方法二：基于堆排序

```java
public class Solution2 {

    int []nums;
    int len;
    private void swap(int[]arr,int a,int b){
        int t=arr[a];
        arr[a]=arr[b];
        arr[b]=t;
    }
    //在i的左右子树已经是堆的情况下，通过元素的下降来建堆
    private void heapify(int i,int n){  //i~n进行下降
        while(i<n){
            int lchild=2*i+1,rchild=2*i+2;
            int p=i;    //p指向的节点在三者之中最大
            if(lchild<n && nums[lchild]>nums[p]){
                p=lchild;
            }
            if(rchild<n && nums[rchild]>nums[p]){
                p=rchild;
            }
            if(i==p){   //说明建堆已经提前完成
                break;
            }
            swap(nums,i,p);
            i=p;
        }
    }

    private void buildHeap(int n){  //对0~n进行堆排序
        for(int i=n/2-1;i>=0;i--){
            heapify(i,n);
        }
    }

    public int findKthLargest(int[] nums, int k) {
        this.nums=nums;
        this.len=nums.length;
        buildHeap(len);
        for(int i=0;i<k;i++){
            swap(nums,0,len-1-i);
            heapify(0,len-1-i);
        }
        return nums[len-k];
    }
}
```

