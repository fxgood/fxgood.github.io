---
date: 2020-07-27
title: 使用优先级队列合并K个升序链表
category: 数据结构
tag: 
- 优先级队列
---

在做[23合并K个升序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)这题时，发现使用优先级队列（堆）能够高效地解决此问题。

思路：直接无脑地将所有节点扔进优先级队列中（小根堆），然后依次取出所有节点，按照头插法构建结果链即可。

<!-- more -->

注意：这里C++优先级队列对于自定义数据类型的比大小的实现，是通过一个结构体内实现`()`运算符重载来实现的。

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */


struct cmp{
    bool operator()(ListNode*&p1,ListNode*&p2){
        return p1->val<p2->val;
    }
};

class Solution {
private:
    priority_queue<ListNode*,vector<ListNode*>,cmp>que;	 	//传入自定义比较器cmp
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        //将所有节点指针，放入大根堆中
        for(int i=0;i<lists.size();i++){
            ListNode*p=lists[i];
            while(p){
                que.push(p);
                p=p->next;
            }
        }
        ListNode*res=new ListNode(0);
        while(!que.empty()){
            //头插法
            ListNode*t=que.top();
            que.pop();
            t->next=res->next;
            res->next=t;
        }
        return res->next;
    }
};
```