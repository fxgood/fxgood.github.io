---
title: leetcode-监控二叉树
category: 算法
tag:
- leetcode
- 动态规划
---

[leetcode监控二叉树](https://leetcode-cn.com/problems/binary-tree-cameras/)

这是一题树形dp题目，可以像一般的dp题目一样分解子问题，把该题目想象成**自下而上**构建一颗完整的二叉树。只不过dp[i-1]只有一条路，而树形dp[i-1]有两条路。通过后序遍历，一个节点获取两个子节点的信息后，如何最优化自己在不同状态下的结果，这是需要考虑的。

<!--more-->

思路：

首先，递归中我们只考虑自己，那么就有两种状态：

1. 当前节点**放摄像头**
2. 当前节点**不放摄像头**

对于`状态1`来说，当前节点的左右子树可以是以下状态

- **放摄像头**

- **不放摄像头，但是子树有摄像头覆盖了自己**

- **不放摄像头，且子树没有覆盖自己**

对于`状态2`来说，当前节点的左右子树可以是以下状态

- 其中一颗子树**放摄像头**，另一颗子树**放摄像头**或**不放摄像头，但是子树有摄像头覆盖了自己**



<font color='red'>综上所述，对于一个节点，我们一共需要三种状态</font>

1. **放摄像头**
2. **不放摄像头，但是被子树覆盖**
3. **不放摄像头，且没有被子树覆盖**



注意：

1. 对于空节点来说，等价于**没有摄像头，且被子树覆盖**（假设被覆盖意味着不需要父亲的摄像头）
   - 更明确地说是，若有情况如下，`ans[2]=left[1]+right[1];`如果把空节点设为没有被子树覆盖，那么这里会出错。

![image-20220421135901626](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220421135901626.png)

2. 主函数中，最后返回的是`min(ans[0],ans[1])`而不是三者最小，因为ans[3]下root没有被覆盖。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public int minCameraCover(TreeNode root) {
        int[]ans=dp(root);
        return min(ans[0],ans[1]);
    }
    int min(int...arr){
        int ans=0x3fffffff;
        for(int a:arr)
            ans=Math.min(ans,a);
        return ans;
    }
    //ans[0]:当前节点放摄像头
    //ans[1]:当前节点不放摄像头，但是被子树至少之一覆盖
    //ans[2]:当前节点不放摄像头，且没有被子树覆盖
    int[] dp(TreeNode t){
        int[]ans=new int[3];
        if(t==null){
            ans[0]=0x3fffffff;
            ans[1]=0;
            ans[2]=0x3fffffff;
            return ans;
        }
        int[]left=dp(t.left);
        int[]right=dp(t.right);
        ans[0]=1+min(left)+min(right);
        ans[1]=min(left[0]+min(right[0],right[1]),right[0]+min(left[0],left[1]));
        ans[2]=left[1]+right[1];
        return ans;
    }


}
```

