---
title: K个一组反转链表
category: 算法
tag:
- leetcode
---

[leetcode](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

递归方法，其中结合了一般反转链表的非递归写法

```java
public class Solution {

      public ListNode reverseKGroup(ListNode head, int k) {
        //如果长度不足k那么直接返回
        int cnt=k;
        ListNode p=head;
        while(p!=null && cnt>0){
            cnt--;
            p=p.next;
        }
        if(cnt>0)
            return head;
        //否则递归调用
        ListNode tail=reverseKGroup(p,k);
        ListNode pre=tail;
        p=head;
        while(k>0){
            k--;
            ListNode t=p.next;
            p.next=pre;
            pre=p;
            p=t;
        }
        return pre;
    }

}
```

