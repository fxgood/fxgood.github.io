---
title: leetcode N皇后问题
date: 2021-03-29
category: 算法
excerpt: 解决完N皇后问题后，将思路加以记录
---

解决完N皇后问题后，将思路加以记录
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210329215522576.png)
首先我整体的方法使用的是`DFS+回溯`，每次递归对第k行，在哪一列放置Queen进行处理。函数主要有：辅助函数`assist`，冲突位置记录函数`choose`，冲突位置回溯函数`backTrace`，其中辅助函数完成主要任务。

- 对于辅助函数，终止条件是递归到第n行（只有0\~n-1行，递归到第n行说明0\~n-1行都成功放置了皇后）
- 辅助函数的递归+回溯的逻辑是
	- 在当前行一个无冲突位置放置皇后（记录当前行放置的皇后的位置，同时在mark数组种记录因此而产生的冲突）
	- 递归下一行
	- 回溯（撤销当前行放置的皇后，撤掉在mark数组记录的冲突）

- 其中记录冲突有多种方法，我使用的是mark矩阵来记录（切记斜方向上有做对角线和右对角线两种）。
	- 网上还有一种不错的方法，是使用`vector<bool>column(n,false)`，`vector<bool>ldiag(2*n-1,false)`，`vector<bool>rdiag(2*n-1,false)`来记录矩阵的每一列，每个左对角线和右对角线是否已被占用，左右对角线的个数都为`2*n-1`，这是显而易见的。

C++代码如下：

```cpp
class Solution
{
public:
    bool mark[9][9];    //记录放置位置，需要回溯
    vector<vector<string> >ans;
    vector<int>queenPos;    //0~n行皇后放置的位置

    //将放置在该位置产生的冲突记录下来
    void choose(int x,int y,vector<pair<int,int> >&record,int n)
    {
        mark[x][y]=false;
        record.push_back(make_pair(x,y));
        //竖直方向
        for(int i=0-n; i<=n; i++)
        {
            if(i+x>=0 && i+x<n)
            {
                if(mark[i+x][y])
                {
                    mark[i+x][y]=false;
                    record.push_back(make_pair(x+i,y));
                }
            }
        }
        //斜方向（主对角线和副对角线）
        for(int i=0-n; i<=n; i++)
        {
            if(i+x>=0 && i+x<n && i+y>=0 && i+y<n )
            {
                if(mark[i+x][i+y])
                {
                    mark[i+x][i+y]=false;
                    record.push_back(make_pair(x+i,y+i));
                }
            }

        }
        for(int i=0-n; i<=n; i++)
        {
            if(i+x>=0 && i+x<n && y-i>=0 && y-i<n )
            {
                if(mark[i+x][y-i])
                {
                    mark[i+x][y-i]=false;
                    record.push_back(make_pair(x+i,y-i));
                }
            }

        }
    }

    void backTrace(vector<pair<int,int> >&record)
    {
        for(int i=0; i<record.size(); i++)
        {
            int x=record[i].first;
            int y=record[i].second;
            mark[x][y]=true;
        }
    }
    //辅助函数，对第k行进行处理
    void assist(int k,int n)
    {
        //递归终止条件，采用下一轮判断法
        if(k==n)
        {
            vector<string>temp;
            for(int i=0;i<queenPos.size();i++){
                string t="";
                for(int j=0;j<n;j++){
                    if(j!=queenPos[i])
                        t+='.';
                    else
                        t+='Q';
                }
                temp.push_back(t);
            }
            ans.push_back(temp);
            return;
        }
        //递归+回溯
        for(int i=0; i<n; i++)  //放置在第k行可以放置的位置上
        {
            if(mark[k][i])
            {
                queenPos.push_back(i);	//记录第k行选择了第i列放置皇后
                vector<pair<int,int> >record;
                choose(k,i,record,n);   //选中该位置以后，标记产生的冲突位置
                assist(k+1,n);
                queenPos.pop_back();	//撤掉第k行在第i列放置的皇后
                backTrace(record);    //回溯刚才记录的冲突
            }
        }
    }

    vector<vector<string> > solveNQueens(int n)
    {
        for(int i=0; i<9; i++)
            fill(mark[i],mark[i]+9,true);
        assist(0,n);    //从第0行开始
        return ans;

    }
};

```

网上根据列、左右对角线占用记录来求解的方法
```cpp
class Solution {
public:
    int n;
    vector<vector<string> >ans;
    vector<string>chess;
    
    void assist(int k,vector<bool>&column,vector<bool>&ldiag,vector<bool>&rdiag){
        //递归终止条件
        if(k==n){
                ans.push_back(chess);
            return;
        }
        //递归+回溯
        for(int i=0;i<n;i++){   //对第k层的第i列进行判断，是否能够放置Queen
            //计算该位置是第列，第几条左对角线，第几条右对角线
            int cur_col=i,cur_ldiag=(i-k)+n-1,cur_rdiag=i+k;
            if(!column[cur_col] || !ldiag[cur_ldiag] || !rdiag[cur_rdiag])
                continue;
            //选中该位置
            column[cur_col]=false;
            ldiag[cur_ldiag]=false;
            rdiag[cur_rdiag]=false;
            string temp="";
            for(int j=0;j<n;j++)
                if(j==i)
                    temp+='Q';
                else
                    temp+='.';
            chess.push_back(temp);
            //递归
            assist(k+1,column,ldiag,rdiag);
            //回溯
            column[cur_col]=true;
            ldiag[cur_ldiag]=true;
            rdiag[cur_rdiag]=true;
            chess.pop_back();
        }
    }
    vector<vector<string> > solveNQueens(int n) {
        this->n=n;
        vector<bool>column(n,true);
        vector<bool>ldiag(2*n-1,true);
        vector<bool>rdiag(2*n-1,true);
        assist(0,column,ldiag,rdiag);
        return ans;
    }
};

```