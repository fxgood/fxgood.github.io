---
title: Floyd判圈法
date: 2021-03-18
category: 算法
tag:
- 双指针
---

Floyd判圈法用于判断一个链表中是否有环并找到环首位置，其算法分为两个部分。

<!--more-->

### 第一部分：判断是否有环
[141环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)
其算法应用龟兔赛跑的思想，使用一个slow和fast指针初始都指向链表第一个节点，slow每次向前走一步，fast向前走两步。如果链表无环，那么fast会先走到NULL节点。如果有环，那么当slow和fast都进入环的时候，由于fast比slow走的快，fast总会追上slow。C++代码如下：
```cpp
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode*slow=head,*fast=head;
        while(slow && fast && fast->next){
            slow=slow->next;
            fast=fast->next->next;
            if(slow==fast)
                return true;
        }
        return false;
    }
};
```
### 第二部分：找到环开始的节点
[142环形链表II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)
当fast和slow相遇的时候，将fast调到链表开头节点，每次fast和slow各向前走一步，直到相遇。相遇时指向的节点即是环开始的节点。

```cpp
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    //floyd判圈法
    public ListNode detectCycle(ListNode head) {
        if(head==null)
            return null;
        ListNode slow=head,fast=head;
        do{
            if(fast.next!=null && fast.next.next!=null)
                fast=fast.next.next;
            else
                return null;
            slow=slow.next;
        }while(slow!=fast);
        slow=head;
        while(slow!=fast){
            slow=slow.next;
            fast=fast.next;
        }
        return slow;
    }
}
```

原理如下：

![image-20220501231020537](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/image-20220501231020537.png)