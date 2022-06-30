---
title: K站中转内最便宜的航班
date: 2021-08-13
category: 算法
tag: 
- leetcode
- 回溯
- dfs
---

[leetcode 787 K站中转内最便宜的航班](https://leetcode-cn.com/problems/cheapest-flights-within-k-stops/)

# 方法一：改进的BFS，使用优先级队列(超时）
一般遇到最短路径（最少步骤、最近距离）的题目，要下意识地想到用BFS。

这一题是带权的，直接bfs不好写，可以借助于**优先级队列**，这一题中需要用到小根堆，每次选择距离src最近的进行处理

<!--more-->

```cpp
class Node{
public:
    int N;  //号
    int K;  //与src之间有多少个中转节点
    int D;  //与src之间的距离
    Node(int n,int k,int d):N(n),K(k),D(d){}
    bool operator<(const Node &node)const{
        return D>node.D;    //小根堆
    }
};

class Solution {
public:
    int findCheapestPrice(int n, vector<vector<int>>& flights, int src, int dst, int k) {
        vector<vector<int>>edges(n,vector<int>(n,-1));
        for(auto&a:flights){
            edges[a[0]][a[1]]=a[2];
        }
        priority_queue<Node>que;
        que.push(Node(src,-1,0));
        while(!que.empty()){
            Node first=que.top();
            que.pop();
            if(first.N==dst && first.K<=k){
                return first.D;
            }
            if(first.K>=k)
                continue;
            //将后续放入
            for(int i=0;i<n;i++){
                if(edges[first.N][i]!=-1){
                    que.push(Node(i,first.K+1,first.D+edges[first.N][i]));
                }
            }      
        }
        return -1;
    }
};
```
**注意**：在这一题中，某些用例下会超时。

# 方法二：dfs+回溯+剪枝（超时）
```cpp
class Solution {
public:
    int des,n;
    vector<vector<int>>edge;
    int findCheapestPrice(int n, vector<vector<int>>& flights, int src, int dst, int k) {
        //邻接矩阵
        edge=vector<vector<int>>(n,vector<int>(n,0x3f3f3f3f));
        for(auto&a:flights){
            edge[a[0]][a[1]]=a[2];
        }
        des=dst;
        this->n=n;
        int ans=help(src,k);
        return ans!=0x3f3f3f3f?ans:-1;
    }
    //src节点，在k个中转限制下到达终点，所需的最小费用（如果不能则返回0x3f3f3f3f)
    int help(int src,int k){
        //k不可能满足了
        if(k<-1)
            return 0x3f3f3f3f;
        //走到终点
        if(src==des){
            return k>=-1?0:0x3f3f3f3f;
        }
        int ans=0x3f3f3f3f;
        //寻找下一个节点
        for(int i=0;i<n;i++){
            if(edge[src][i]!=0x3f3f3f3f && edge[src][i]<ans){
                ans=min(ans,edge[src][i]+help(i,k-1));
            }
        }
        path.erase(src);
        return ans;
    }
};
```

# 方法四：Bellman-Ford 算法