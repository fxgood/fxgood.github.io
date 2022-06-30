---
title: 两道二叉树的公共祖先
category: 算法
tag:
- leetcode
---

[leetcode 二叉搜索树的最近公共祖先](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-zui-jin-gong-gong-zu-xian-lcof/)

牢牢把握一件事：pq在root异侧则说明root是pq的最近公共祖先

小技巧：先用一个递归保证p.val小于q.val，后续处理就很方便了，很棒！

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    //二叉搜索树的特点：左子树都小于root，右子树都大于root
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        int v=root.val;
        int pv=p.val;
        int qv=q.val;
        if(pv>qv)   //保证p的值小于q
            return lowestCommonAncestor(root,q,p);
        if(pv>v)
            return lowestCommonAncestor(root.right,p,q);
        else if(qv<v)
            return lowestCommonAncestor(root.left,p,q);
        return root;
    }
}
```



[二叉树的最近公共祖先](https://leetcode-cn.com/problems/er-cha-shu-de-zui-jin-gong-gong-zu-xian-lcof/)

方法一：最好的方法

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    //若t是ab的最近公共祖先，则ab在t的异侧
    //返回pq的最近公共祖先或者p或者q或者null
    //抽象的来说，就是把pq在树中，往上拎
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) { 
        if(root==null)
            return null;  
        if(root==p || root==q)
            return root;
        TreeNode left=lowestCommonAncestor(root.left,p,q);
        TreeNode right=lowestCommonAncestor(root.right,p,q);
        if(left!=null && right!=null)   //说明root是最近公共祖先
            return root;
        if(left!=null)
            return left;
        if(right!=null)
            return right;
        return null;
    }   
}
```

方法二：

递归+位运算。如果以某一节点为根的树包含了p和q，那么该节点是p和q的祖先；后续遍历，找到最早出现的祖先即可。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    TreeNode ans;
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        find(root,p,q);
        return ans;
    }   
    int find(TreeNode t,TreeNode p,TreeNode q){
        if(ans!=null || t==null)   //说明已经找到了
            return 0;
        int l=find(t.left,p,q);
        if(ans!=null)	//剪枝
            return 0;
        int r=find(t.right,p,q);
        if(ans!=null)	//剪枝
            return 0;
        int s=0;
        if(t==p)
            s|=1;
        if(t==q)
            s|=2;
        s|=l;
        s|=r;
        if(s==3 && ans==null){
            ans=t;
        }
        return s;
    }

}
```

