---
title: LRU缓存
category: 算法
tag: 
- leetcode
excerpt: Least recently used 最近最少使用,即最近使用的最后替换.
---

[leetcode146LRU缓存](https://leetcode-cn.com/problems/lru-cache/)，这题可以直接用Java的LinkedHashMap来写，或者自己实现hash+双向链表。

<!--more-->

首先分析问题，题目有两个需求，一个是O(1)时间找到key-value；一个是根据LRU替换规则，需要在各key-value之间保持某种顺序

- O(1)时间访问-->哈希表
- 保持一定顺序-->双向链表维护顺序

在Java中LinkedHashMap底层是hash表+双向链表，因此可以直接继承LinkedHashMap，或者自己手动写一个也可以。

# 方法一：使用Java的LinkedHashMap

详解见leetcode [题解](https://leetcode-cn.com/problems/lru-cache/solution/yuan-yu-linkedhashmapyuan-ma-by-jeromememory/)

```java
class LRUCache extends LinkedHashMap<Integer, Integer>{
    private int capacity;
    
    public LRUCache(int capacity) {
        super(capacity, 0.75F, true);
        this.capacity = capacity;
    }

    public int get(int key) {
        return super.getOrDefault(key, -1);
    }
	//可不写
    public void put(int key, int value) {
        super.put(key, value);
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
        return size() > capacity; 
    }
}
```

主要就是重写removeEldestEntry函数，实现自定义的删除最老元素回调函数的时机。

# 方法二：手动实现LRU

注意：删除节点时要同时在hash表和双向链表中删除

```java
import java.util.HashMap;
import java.util.LinkedList;
import java.util.Map;

/*首先分析有哪些动作
* 1. get：如果没有返回-1，如果有返回value，并且将数据在链表中的位置放到开头
* 2. put，如果有，更新值并将节点放到链表开头；
*           如果没有，则放到链表开头（若空间不足不需要先删除链表尾部元素，
*           并在hash表中删除）*/

public class LRUCache {
    //成员内部类
    class Node{
        int key,value;
        Node pre,next;
        Node(int value){
            this.value=value;
            pre=null;
            next=null;
        }
        Node(int key,int value){
            this.key=key;
            this.value=value;
        }
        Node(int key,int value,Node pre,Node next){
            this.key=key;
            this.value=value;
            this.pre=pre;
            this.next=next;
        }
    }
    //双向链表
    Node head;
    Node tail;
    //哈希表
    Map<Integer,Node> hash=new HashMap<>();
    private int capacity;
    private int count=0;
    public LRUCache(int capacity){
        this.capacity=capacity;
        head=new Node(-1);
        tail=new Node(-1);
        head.next=tail;
        tail.pre=head;
    }

    public int get(int key){
        if(!hash.containsKey(key))
            return -1;
        Node t=hash.get(key);
        int val=t.value;
        deleteNode(t);
        moveToHead(t);
        return val;
    }

    public void put(int key,int value){
        if(hash.containsKey(key)){
            Node t=hash.get(key);
            t.value=value;
            deleteNode(t);
            moveToHead(t);
        }else{
            if(count<capacity){
                count++;
            }else{
                hash.remove(tail.pre.key);
                deleteNode(tail.pre);

            }
            Node t=new Node(key,value);
            hash.put(key,t);
            moveToHead(t);
        }
    }

    //将某个节点移动到双向链表的开头
    private void moveToHead(Node t){
        t.next=head.next;
        head.next=t;
        t.next.pre=t;
        t.pre=head;
    }
    //将某个节点剥离双向链表
    private void deleteNode(Node t){
        t.next.pre=t.pre;
        t.pre.next=t.next;
    }
}

```

