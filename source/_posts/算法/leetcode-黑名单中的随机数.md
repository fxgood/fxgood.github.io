---
title: leetcode 黑名单中的随机数
date: 2021-08-04
category: 算法
tag:
- leetcode
---

[leetcode 710 黑名单中的随机数](https://leetcode-cn.com/problems/random-pick-with-blacklist/)

涉及到等概率的问题，一般都得使用数组（用随机数作下标等概率随机返回一个数）来解决，数组即为一种顺序存储的结构，因此**逻辑上顺序**，也能够满足要求。

<!--more-->

下面的图片用`√`表示有效数字，`X`表示在黑名单中的数字。其中，虚线是，最终将所有有效数字挪到左侧以后，和右侧黑名单数字的分界线（用总数量减去黑名单数量，即可得到最终有效数字的数量）

如图，根据虚线划分位置的数学原因，左侧`X`的数量和右侧`√`数量相同，因此只要将左侧的`X`映射到右侧每个`√`，即可完成任务。后序在虚线左侧随机返回一个下标值，就达到了题目的要求。
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/0dfb328e4ba34a6b9e608fb02c05e5f8.png)
在代码实现上：
1.先将所有黑名单数字放入map中，last指向数组末尾元素
2.遍历黑名单数字，如果在虚线右侧，则什么都不做；如果在虚线左侧，那么**将last左移至第一个为√**的位置，然后在map中添加映射，最后`last--`

```cpp
class Solution {
private:
    int split;    //有效数字的总数量
    map<int,int>mp;
public:
    Solution(int n, vector<int>& blacklist) {
        split=n-blacklist.size();
        int last=n-1;
        //先将所有黑名单数字记录，因为last需要向左找到第一个不在黑名单中的数字
        for(auto&i:blacklist)
            mp[i]=-1;
        //只有黑名单数字，才需要映射
        for(auto&i:blacklist){
            if(i<split){
                while(mp.find(last)!=mp.end())
                    last--;
                mp[i]=last;
                last--;
            }
        }
        srand((unsigned)time(NULL));
    }
    
    int pick() {
        int ans=rand()%split;
        //如果是黑名单数字，则需要映射
        if(mp.find(ans)!=mp.end())
            return mp[ans];
        return ans;    
    }
};
```