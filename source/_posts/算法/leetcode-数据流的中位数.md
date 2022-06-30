---
title: leetcode-数据流的中位数
category: 算法
tag:
- leetcode
---

[leetcode](https://leetcode-cn.com/problems/shu-ju-liu-zhong-de-zhong-wei-shu-lcof/)

精髓是用两个堆来保存大的一半和小的一半的数字。

凡是遇到奇怪的数据结构的题目，首先要想到堆、hash这些。。

```java
class MedianFinder {

    PriorityQueue<Integer>que1=new PriorityQueue<>((x,y)->(y-x));   //大根堆
    PriorityQueue<Integer>que2=new PriorityQueue<>();   //小根堆
    int n1=0,n2=0;
    /** initialize your data structure here. */
    public MedianFinder() {

    }

    public void addNum(int num) {
        /*这样的方式存入，可以保证que1中的元素全部小于等于que2中的元素*/
        if(n1==n2){
            que2.offer(num);
            que1.offer(que2.poll());
            n1++;
        }else{
            que1.offer(num);
            que2.offer(que1.poll());
            n2++;
        }
    }


    public double findMedian() {
        if(n1==n2)
            return (que1.peek()+que2.peek())/2.0;
        return que1.peek();
    }
}

/**
 * Your MedianFinder object will be instantiated and called as such:
 * MedianFinder obj = new MedianFinder();
 * obj.addNum(num);
 * double param_2 = obj.findMedian();
 */
```

