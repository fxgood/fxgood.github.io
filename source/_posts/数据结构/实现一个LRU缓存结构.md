---
date: 2020-07-19
title: 实现一个LRU缓存结构
category: 数据结构
tag: 
- 哈希
---

LRU：最近最少使用算法。LRU缓存算法，由哈希表和双向链表构成，能以O(1)时间复杂度获取缓存的键值，也能以O(1)的时间复杂度存储键值。
- Java中有内置类型`LinkedHashMap`，可以直接用于LRUCache的实现。C++没有，需要手动实现，这里我手动实现一个。
- leetcode有一题LRU题目[LRU缓存机制](https://leetcode-cn.com/problems/lru-cache/)

<!-- more -->

```cpp
//双向链表+哈希表
struct Node{
    int key,val;
    Node*pre,*next;
    Node(int k,int v):key(k),val(v),pre(NULL),next(NULL){}
};

class DoubleList{
    public:
        DoubleList(){
            size=0;
            head=new Node(0,0);
            tail=new Node(0,0);
            head->next=tail;
            tail->pre=head;
        }
        //在链表尾部添加节点
        void addTail(Node*x){
            x->next=tail;
            x->pre=tail->pre;
            tail->pre->next=x;
            tail->pre=x;
            size++;
        }
        //删除某个节点(根据地址)
        void deleteNode(Node*x){
            x->pre->next=x->next;
            x->next->pre=x->pre;
            size--;
            //return x;
        }
        //删除首节点，并返回其键值，用于反向删除map中的键
        int deleteFirst(){
            int key=head->next->key;
            deleteNode(head->next);
            return key;
        }
        int getSize(){
            return size;
        }
    private:
        int size;
        Node*head,*tail;    //tail处是最近访问的
};

class LRUCache {
public:
    LRUCache(int capacity) {
        this->capacity=capacity;
    }
    
    int get(int key) {
        if(node_map.find(key)==node_map.end())
            return -1;
        Node*p=node_map[key];
        makeRecent(p);
        return p->val;
    }
    
    void put(int key, int value) {
        //如果已经存在，则更新值
        if(node_map.find(key)!=node_map.end()){
            Node*p=node_map[key];
            makeRecent(p);
            p->val=value;
            return;
        }
        //不存在的情况下：
        //如果满了
        if(capacity==cache.getSize()){
            removeLRU();
        }
        Node*p=new Node(key,value);
        cache.addTail(p);
        //放入哈希表
        node_map[key]=p;
    }

    //将某个节点提升到recent
    void makeRecent(Node*x){
        cache.deleteNode(x);
        cache.addTail(x);
    }
    //删除LRU节点缓存
    void removeLRU(){
        int key=cache.deleteFirst();
        node_map.erase(key);
    }
private:
    int capacity;
    DoubleList cache;
    unordered_map<int,Node*>node_map;
};
```

```java
class LRUCache {
    int cap;
    LinkedHashMap<Integer, Integer> cache = new LinkedHashMap<>();
    public LRUCache(int capacity) { 
        this.cap = capacity;
    }
    
    public int get(int key) {
        if (!cache.containsKey(key)) {
            return -1;
        }
        // 将 key 变为最近使用
        makeRecently(key);
        return cache.get(key);
    }
    
    public void put(int key, int val) {
        if (cache.containsKey(key)) {
            // 修改 key 的值
            cache.put(key, val);
            // 将 key 变为最近使用
            makeRecently(key);
            return;
        }
        
        if (cache.size() >= this.cap) {
            // 链表头部就是最久未使用的 key
            int oldestKey = cache.keySet().iterator().next();
            cache.remove(oldestKey);
        }
        // 将新的 key 添加链表尾部
        cache.put(key, val);
    }
    
    private void makeRecently(int key) {
        int val = cache.get(key);
        // 删除 key，重新插入到队尾
        cache.remove(key);
        cache.put(key, val);
    }
}

```