---
title: leetcode-正则表达式匹配
category: 算法
tag:
- leetcode
- 动态规划
date: 2021-08-18
---

[leetcode 10 正则表达式匹配](https://leetcode-cn.com/problems/regular-expression-matching/)
这一题的关键在于
第一，处理好dp的**初始状况**（`i==0`, `j==0`的情况）
第二，**对复杂问题进行分类讨论**。思考清楚当前匹配字符为`*`时的情况，`*`可以匹配**0个，或者多个**前一位的字符，抓住这个特点即可写出状态转移方程

`*`匹配0次：如果`dp[i][j-2]==true`，则`dp[i][j]=true`
`*`匹配多次：需要两个条件，第一：`dp[i-1][j]==true`，第二`s`当前字符必须和p中`*`的前一个字符匹配。（思考一下，这是一种有趣的递归写法）

<!--more-->

```cpp
class Solution {
public:
    bool isMatch(string s, string p) {
        int len1=s.size(),len2=p.size();
        //dp[i][j],s长度为i，p长度为j时，二者能否匹配
        vector<vector<int>>dp(len1+1,vector<int>(len2+1,false));
        for(int i=0;i<=len1;i++){
            for(int j=0;j<=len2;j++){
                if(i==0 && j==0){
                    dp[0][0]=true;
                    continue;
                }
                if(j==0){
                    dp[i][j]=false;
                    continue;
                }
                if(i==0){
                    if(j>=2 && p[j-1]=='*' && dp[0][j-2])
                        dp[i][j]=true;
                    else
                        dp[i][j]=false;
                    continue;
                }
                if(p[j-1]=='.')
                    dp[i][j]=dp[i-1][j-1];
                else if(p[j-1]!='*'){
                    /*必须前边匹配上了，本位置也匹配上了才行*/
                    dp[i][j]=dp[i-1][j-1] && s[i-1]==p[j-1];
                }else{
                    //*匹配0次，有*代表p至少长度为2
                    if(dp[i][j-2])
                    {
                         dp[i][j]=true;
                         continue;
                    }   
                    //*匹配多次
                    if(dp[i-1][j] && (s[i-1]==p[j-2] || p[j-2]=='.') ){
                        dp[i][j]=true;
                        continue;
                    }
                    dp[i][j]=false;
                }
            }
        }
        return dp[len1][len2];
    }
};
```