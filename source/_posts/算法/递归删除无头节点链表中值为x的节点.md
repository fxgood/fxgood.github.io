---
title: 递归删除不带头节点的链表中所有值为x的节点
date: 2021-08-02
category: 算法
tag:
- 递归
---

题目如标题所示，这一题有个很关键的问题，在于**传参引用，然后修改传入指针的指向**，只有这样才能完成递归操作。

值得一提的是，按我目前的java知识水平来理解，Java似乎无法完成这个算法，因为**java只有值传递**，因此无法完成这个算法。

<!--more-->

```cpp
//递归删除没有头节点的链表中所有值为x的节点
void deleteX(Node *p, int x) {
    if (!p)
        return;
    if (p->val == x) {
        //Node*tmp=p;   //释放节点
        p = p->next;	//修改传入指针的指向
        //free(tmp);
        deleteX(p, x);   /*注意这里传入的参数是p，不是p->next，否则会造成漏处理一个节点*/
    } else {
        deleteX(p->next, x);
    }
}
```

