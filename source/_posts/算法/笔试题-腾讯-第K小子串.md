---
title: 第K小子串
category: 算法
tag:
- 笔试题
- 腾讯
excerpt: 这题在时间和空间上的优化都值得品味!
---

[牛客-腾讯-第K小子串](https://www.nowcoder.com/profile/272852641/test/57833001/1795713#summary)

我的思路是

- 双层for循环分割子串
- 用大根堆来装最多k个串,用set来处理堆中的重复问题(只去重堆内的元素,否则会空间复杂度过高)

我本以为我的方法已经足够巧妙,但是时间还是超了,最后看题解:

- 一条重要结论: **第k小子串的长度一定是小于等于K的!**
- 借助这个结论可以大大优化双层for循环的时间

```java
import java.util.*;
public class Main{
    public static void main(String[]args){
        Scanner sc=new Scanner(System.in);
        String s=sc.next();
        int m=sc.nextInt();
        int n=s.length();
        PriorityQueue<String>que=new PriorityQueue<>((a,b)->-a.compareTo(b));
        Set<String>set=new HashSet<>();
        for(int i=0;i<n;i++)
            for(int j=i;j<n && j-i+1<=m;j++){
                String t=s.substring(i,j+1);
                if(!set.contains(t)){
                    set.add(t);
                    que.offer(t);
                    if(que.size()>m){
                        set.remove(que.poll());
                    }
                }
            }
        System.out.println(que.poll());
    }
}
```

