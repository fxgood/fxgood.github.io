---
title: leetcode-划分k个相等的子集
date: 2021-08-19
category: 算法
tag:
- leetcode
---

[leetcode 698 划分为k个相等的子集](https://leetcode-cn.com/problems/partition-to-k-equal-sum-subsets/)

解题之前先将题目等效为更容易求解的问题，这一题可以转换为一组数放入若干个桶中，使每个桶中数字的和均为`target`。

这一题有两种解题视角：
1.从数字角度出发，每个数字选择一个桶放入
2.从桶角度出发，每个桶选择若干数字放入自身

<!--more-->


解法1：
该解法中，事先对数组进行由大到小的排序，可以大幅提升效率，原因在于：如果本轮的一系列选择最终会导致溢出，那么应该让溢出早点发生，不浪费算力，因此大的放前边可以使迟早会溢出的一组数早点溢出，提高效率。
```java
class Solution {
    private int target;
    private int[] basket;
    //把数放到四个篮子里
    private int ans = 0;
    int[] nums;
    int k;

    public boolean canPartitionKSubsets(int[] nums, int k) {
        this.k = k;
        this.nums = nums;
        int len = nums.length;
        if (k > len)
            return false;
        basket = new int[k];
        Arrays.fill(basket, 0);
        int sum = Arrays.stream(nums).sum();
        if (sum % k != 0)
            return false;
        target = sum / k;
        //写一个冒泡排序，时间复杂度O(N^2)
        for(int i=0;i<nums.length-1;i++){
            for(int j=0;j<nums.length-1-i;j++){
                if(nums[j]<nums[j+1]){
                    int t=nums[j];
                    nums[j]=nums[j+1];
                    nums[j+1]=t;
                }
            }
        }
        dfs(0);
        return ans == 1;
    }

    private void dfs(int t) {
        //if ans is already true, then stop recursion
        if (ans == 1)
            return;
        if (t == nums.length) {
            boolean flag = true;
            //check k baskets
            for (int n : basket) {
                if (n != target) {
                    flag = false;
                    break;
                }
            }
            if (flag)
                ans = 1;
            return;
        }
        //choose the basket
        for (int i = 0; i < basket.length; i++) {
            if (nums[t] + basket[i] <= target) {
                basket[i] += nums[t];
                dfs(t + 1);
                basket[i] -= nums[t];
            }
        }
    }


}
```

解法2：
```java
class Solution {
    private int n,target,k;
    private int[] bucket,nums;
    private int tag=0;  //tag for which bucket to use
    private boolean []canBeChose;
    public boolean canPartitionKSubsets(int[] nums, int k) {
        n=nums.length;
        this.nums=nums;
        this.k=k;
        if(k>n)
            return false;
        int sum=Arrays.stream(nums).sum();
        if(sum%k!=0)
            return false;
        target=sum/k;
        bucket=new int[k];
        Arrays.fill(bucket,0);/*initialize zero for every bucket*/
        canBeChose=new boolean[n];
        Arrays.fill(canBeChose,true);
        return dfs(0,0);
    }

    private boolean dfs(int bucketNum,int index){
        /*k个桶都选完了*/
        if(bucketNum==k){
            for(int i:bucket)
                System.out.print(i+" ");
            System.out.println();
            return true;
        }
        //某个桶已经选完了，看下一个桶
        if(index==n){
            if(bucket[bucketNum]!=target)
                return false;
            return dfs(bucketNum+1,0);
        }
        //当前桶已经选满了
        if(bucket[bucketNum]==target){
            return dfs(bucketNum+1,0);
        }
        //如果该元素已经被选中了
        if(!canBeChose[index])
            return dfs(bucketNum,index+1);
        if(bucket[bucketNum]+nums[index]<=target){
            bucket[bucketNum]+=nums[index];
            canBeChose[index]=false;
            if (dfs(bucketNum,index+1)) {
                return true;
            }
            canBeChose[index]=true;
            bucket[bucketNum]-=nums[index];
        }
        if (dfs(bucketNum,index+1)) {
            return true;
        }
        return false;
    }
}

```