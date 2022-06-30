---
title: 状态压缩dp
date: 2021-05-10
category: 算法
tag: 
- 动态规划
excerpt: 记录状态压缩dp的使用方法和使用案例。
---

# 理解
> 当出现NPC问题，但是题目给出的N在20左右时，一般用状态压缩dp来解决

状态压缩dp本质还是动态规划，只不过是将一些不方便表示的状态用二进制数的形式来表示。
# 例题
## 例题1
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210510213230288.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjI1OTA4OA==,size_16,color_FFFFFF,t_70)
```cpp
#include <iostream>
#include <cstdio>

using namespace std;
typedef long long ll;
int N, K;
ll dp[9][(1 << 9)][82] = {0};   //dp[i][st][j] 表示0~i行共j个国王，第i行状态为st，此时的方案数
//所求结果=sum(dp[.][.][K])
//状态转移方程：dp[i][st][j]=sum( dp[i-1][st2][K-count(st)] )
int countOne(int n) {
    int count = 0;
    while (n) {
        count += (n & 1);
        n >>= 1;
    }
    return count;
}

ll f() {
    //初始化第一行，所有合法状态的方案数都是1
    for (int s = 0; s < (1 << N); s++) {
        if (s & (s << 1)) continue;
        dp[0][s][countOne(s)] = 1;

    }
    //枚举行
    for (int i = 1; i < N; i++)
        //枚举当前行状态
        for (int s = 0; s < (1 << N); s++) {
            if (s & (s << 1)) continue;
            for (int j = countOne(s); j <= K; j++) {    //0~i行的king数量
                for (int s2 = 0; s2 < (1 << N); s2++) {
                    if (s2 & (s2 << 1)) continue;
                    if (s & s2) continue;
                    if ((s << 1) & s2) continue;
                    if (s & (s2 << 1)) continue;
                    dp[i][s][j] += dp[i - 1][s2][j - countOne(s)];
                }
            }
        }
    long long ans = 0;
    for (int i = 0; i < (1 << N); i++)
        ans += dp[N - 1][i][K];
    return ans;
}

int main() {
    scanf("%d%d", &N, &K);
    cout << f();
    return 0;
}

```
## 例题2
> http://acm.hdu.edu.cn/showproblem.php?pid=5418  杭电OJ

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210511155114621.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjI1OTA4OA==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210511155125630.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjI1OTA4OA==,size_16,color_FFFFFF,t_70)
```cpp
#include <iostream>
#include <algorithm>
using namespace std;
const int INF = 1e9 + 7;
int T, n, m;
int dp[(1 << 16)][16]; //dp[i][j]表示，路径为i，终点为j时，最少的油耗
int oilCost[16][16];   //油耗
int f() {
    //处理特殊情况
    if (n == 1)
        return 0;
    //规定从0节点出发
    dp[1][0] = 0;
    //开始dp
    for (int st = 1; st < (1 << n); st++)    //枚举不同访问状态
        for (int i = 0; i < n; i++)
            if ((1 << i) & st)    //枚举已访问节点
                //枚举未访问节点
                for (int j = 0; j < n; j++)
                    if (!((1 << j) & st)) {
                        dp[st | (1 << j)][j] = min(dp[st | (1 << j)][j], dp[st][i] + oilCost[i][j]);
                    }
    //整理结果
    int ans = INF;
    for (int i = 1; i < n; i++) {
        ans = min(ans, dp[(1 << n) - 1][i] + oilCost[0][i]);
    }
    return ans;
}

void init() {
    //初始为INF
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            if (i == j)
                oilCost[i][j] = 0;
            else
                oilCost[i][j] = INF;
    while (m--) {
        int a, b, cost;
        scanf("%d%d%d", &a, &b, &cost);
        if(oilCost[a-1][b-1]<=cost)	//这一步不知道为什么，但是不加就不通过oj
            continue;
        oilCost[a - 1][b - 1] = cost;
        oilCost[b - 1][a - 1] = cost;
    }
    //floyd计算任意两点之间的最少油耗
    for (int i = 0; i < n; i++)
        for (int j = i + 1; j < n; j++)
            for (int k = 0; k < n; k++)
                if (oilCost[i][k] + oilCost[k][j] < oilCost[i][j]) {
                    oilCost[j][i] = oilCost[i][k] + oilCost[k][j];
                    oilCost[i][j] = oilCost[i][k] + oilCost[k][j];
                }
    //打印floyd结果
//    for (int i = 0; i < n; i++) {
//        for (int j = 0; j < n; j++)
//            cout << oilCost[i][j] << " ";
//        cout << endl;
//    }

    //重置dp
    for (int i = 0; i < (1 << n); i++)
        for (int j = 0; j < n; j++)
            dp[i][j] = INF;
}

int main() {
    scanf("%d", &T);
    while (T--) {
        scanf("%d%d", &n, &m);
        init();
        cout << f()<<endl;
    }
    return 0;
}
```
## 例题3
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210511185532689.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjI1OTA4OA==,size_16,color_FFFFFF,t_70)
```cpp
#include <iostream>
#include <cstdio>

using namespace std;
int count = 0;
const int Mod = 1e9;
int M, N;
int fieldState[12] = {0};
int dp[12][(1 << 12)];    //dp[i][st]表示第i行状态为st时，0~i行的方案数
void init() {
    cin >> M >> N;
    int t;
    for (int i = 0; i < M; i++) {
        for (int j = 0; j < N; j++) {
            scanf("%d", &t);
            fieldState[i] <<= 1;
            fieldState[i] += t;
        }
    }
}

bool checkLine(int st, int landLimit) {
    return !(st & (st << 1)) && (landLimit | st) == landLimit;
}

bool checkTwoLines(int st1, int st2) {
    return !(st1 & st2);
}

int f() {
    //初始化dp第一行
    for (int st = 0; st < (1 << N); st++)
        if (checkLine(st, fieldState[0]))
            dp[0][st] = 1;
    //开始dp
    for (int i = 1; i < M; i++)
        for (int st1 = 0; st1 < (1 << N); st1++)
            if (checkLine(st1, fieldState[i]))
                for (int st2 = 0; st2 < (1 << N); st2++)
                    if (checkLine(st2, fieldState[i - 1]) && checkTwoLines(st1, st2)) {
                        dp[i][st1] = (dp[i][st1] + dp[i - 1][st2]) % Mod;
                    }
    //统计结果
    int ans = 0;
    for (int st = 0; st < (1 << N); st++) {
        ans = (ans + dp[M - 1][st]) % Mod;
    }
    return ans;
}

int main() {
    init();
    cout << f();
    return 0;
}
```
## 例题4
![在这里插入图片描述](https://img-blog.csdnimg.cn/202105111856078.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjI1OTA4OA==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021051118561496.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjI1OTA4OA==,size_16,color_FFFFFF,t_70)
```cpp
#include <iostream>
#include <cstdio>
#include <algorithm>

using namespace std;
/*写题目之前先关注：数据大小，所用方法复杂度，边界情况*/
//@Todo 为什么记两行，因为枚举当前行的状态时，要与前两行进行比较
int N, M;
int battle[100];   //记录每一行的战场情况
int goodSt[1000];   //记录在一行中合法的状态
int goodN = 0;  //一行中合法状态的数量
int dp[100][(1 << 10)][1 << 10] = {0};    //dp[i][st1][st2]表示，第i行情形为st1，i-1行情形为st2，此状态下0~i行最多能摆放的炮兵数量
//状态转移方程：dp[i][st1][st2]=max(自身,dp[i-1][st2][st3]+count(st1) ) if( checkLine(st1,st2,st3) )
void init() {
    cin >> N >> M;
//    scanf("%d%d",&N,&M);
    //预处理，将一行中的合法状态存入goodSt中
    for (int st = 0; st < (1 << M); st++) {
        if (!(st & (st << 1)) && !(st & (st << 2)))
            goodSt[goodN++] = st;
    }
    //输入战场情况,1表示平原可摆放
    char c;
    for (int i = 0; i < N; i++) {
        int battle_st = 0;
//        getchar();
        for (int j = 0; j < M; j++) {
            cin>>c;
//            scanf("%c", &c);
            battle_st <<= 1;
            battle_st += (c == 'P');
        }
        battle[i] = battle_st;
    }
}

//检查是否与当前行匹配
bool checkLine(int st, int line) {
    return (line | st) == line;
}

//检查两行之间是否匹配
bool check(int st1, int st2) {
    return !(st1 & st2);
}

//计算一种摆放状态下的部队数
int count(int st) {
    int num = 0;
    while (st) {
        num += (st & 1);
        st >>= 1;
    }
    return num;
}

//处理dp
void f() {
    if (N == 0)
        return;
    //处理第一行
    for (int i = 0; i < goodN; i++) {
        int st = goodSt[i];
        if (checkLine(st, battle[0])) {
            for (int st2 = 0; st2 < (1 << M); st2++)   //枚举-1行状态，其实并不存在，只是配合dp而用
                dp[0][st][st2] = count(st);
        }
    }
    if (N == 1)
        return;
    //处理第二行
    for (int i = 0; i < goodN; i++) {
        int st1 = goodSt[i];
        for (int j = 0; j < goodN; j++) {
            int st2 = goodSt[j];
            if (!check(st1, st2))
                continue;
            dp[1][st1][st2] = count(st1) + count(st2);
        }
    }
    //开始动态规划
    for (int row = 2; row < N; row++) {
        for (int i = 0; i < goodN; i++) { //枚举当前行摆放方式
            int st1 = goodSt[i];
            if (!checkLine(st1, battle[row]))
                continue;
            for (int j = 0; j < goodN; j++) {   //枚举上一行摆放方式
                int st2 = goodSt[j];
                if (!checkLine(st2, battle[row - 1]))
                    continue;
                for (int k = 0; k < goodN; k++) {   //枚举上上行摆放方式
                    int st3 = goodSt[k];
                    if (!checkLine(st3, battle[row - 2]))
                        continue;
                    if (!check(st1, st2) || !check(st1, st3) || !check(st2, st3))
                        continue;
                    dp[row][st1][st2] = max(dp[row][st1][st2], dp[row - 1][st2][st3] + count(st1));
                }
            }
        }
    }
}

//计算结果
int getRes() {
    f();
    //统计结果
    int ans = 0;
    for (int i = 0; i < goodN; i++)
        for (int j = 0; j < goodN; j++)
            ans = max(ans, dp[N - 1][goodSt[i]][goodSt[j]]);
    return ans;
}

int main() {
    init();
    cout << getRes();
    return 0;
}
```