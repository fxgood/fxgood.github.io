---
title: leetcode148-排序链表
category: 算法
excerpt: 这题充分说明了，凡是涉及到链表的排序，归并是效率最高的，可以将问题转化为两个有序链表的合并问题。
---

[leetcode148](https://leetcode.cn/problems/sort-list/)

- 这题充分说明了，凡是涉及到链表的排序，归并是效率最高的，可以将问题转化为两个有序链表的合并问题。
- 此外，自底向上的方法可以避免递归带来的空间复杂度的提升。

自顶向下：时间复杂度O(nlogn)，空间复杂度即递归栈的深度O(logn)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode sortList(ListNode head) {
        if(head==null || head.next==null)
            return head;
        //找到中点
        ListNode slow=head,fast=head;
        while(fast.next!=null && fast.next.next!=null){   //找到中间偏左的节点
            fast=fast.next.next;
            slow=slow.next;
        }
        ListNode t=slow.next;
        slow.next=null;
        ListNode l=sortList(head);  //要断开链表！！！
        ListNode r=sortList(t);
        //合并链表
        return mergeList(l,r);
    }

    ListNode mergeList(ListNode a,ListNode b){
        if(a==null)
            return b;
        if(b==null)
            return a;
        if(a.val<b.val){
            a.next=mergeList(a.next,b);
            return a;
        }else{
            b.next=mergeList(a,b.next);
            return b;
        }
    }
}

```

自底向上:时间复杂度(nlogn)，空间复杂度O(1)

```java
```

