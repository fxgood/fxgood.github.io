---
title: leetcode-地下城游戏
date: 2021-8-11
category: 算法
tag:
- leetcode
- 动态规划
---

这一题让我学到了两个教训：
1. 动态规划先写**状态转移方程**
2. 写动态规划应该思维灵活，有时需要逆向思维

<!--more-->

```cpp
//先写状态转移方程，无论是递归还是动态规划
/*
dp[i][j]=max(1,min(dp[i+1][j],dp[i][j+1])-dungeon[i][j]);
*/
class Solution {
public:
    int calculateMinimumHP(vector<vector<int>>& dungeon) {
        int m=dungeon.size(),n=dungeon[0].size();
        vector<vector<int>>dp(m,vector<int>(n,0x3f3f3f3f));
        for(int i=m-1;i>=0;i--){
            for(int j=n-1;j>=0;j--){
                if(i==m-1 && j==n-1){
                    dp[i][j]=max(1,1-dungeon[i][j]);
                    continue;
                }
                int a=0x3f3f3f3f,b=0x3f3f3f3f;  //想加相乘都小于int范围
                if(i+1<m){
                    a=dp[i+1][j];
                }
                if(j+1<n){
                    b=dp[i][j+1];
                }
                dp[i][j]=max(1,min(a,b)-dungeon[i][j]);
            }
        }
        return dp[0][0];
    }
};
```