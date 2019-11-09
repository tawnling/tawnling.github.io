---
title: luoguP1880 石子合并
top: false
cover: false
toc: true
mathjax: false
date: 2019-02-25 23:57:26
img:
password:
summary:
tags:
- DP
- Algorithm
categories:
- Algorithm
---

## 一、题目内容

**【题目描述】**

在一个圆形操场的四周摆放N堆石子,现要将石子有次序地合并成一堆.规定每次只能选相邻的2堆合并成新的一堆，并将新的一堆的石子数，记为该次合并的得分。

试设计出1个算法,计算出将N堆石子合并成1堆的最小得分和最大得分.

**【输入格式】**

数据的第1行试正整数N,1≤N≤100,表示有N堆石子.第2行有N个数,分别表示每堆石子的个数.

**【输出格式】**

输出共2行,第1行为最小得分,第2行为最大得分.

**【输入样例】**

4  
4 5 9 4  

**【输出样例】**

43  
54

**【说明】**


**【测试网站】**

[luoguP1880](https://www.luogu.org/problemnew/show/P880)

## 二、题目分析

## 三、代码示例

因为石子是围成一圈的，所以将n堆石子展开成2*n,再进行区间dp
从长度为2开始，一直到n，n-2*n 的部分也需要进行dp, dp状态转移方程为：  
dp[i][j] = max(dp[i][k] + dp[k+1][j] + sum[j]-sum[i-1]) (i<k<j-1)
最后答案max(dp[i][i+n-1])。
最小得分亦然。

```cpp
#include <bits/stdc++.h>

using namespace std;
const int maxn = 200 + 10;
const int INF = 0x3f3f3f3f;
int dp1[maxn][maxn],dp2[maxn][maxn];
int a[maxn],sum[maxn] = {0};

int main()
{
   int n;
   scanf("%d",&n);
   for(int i = 1; i <= n; i++)
    {
      scanf("%d",&a[i]);
      a[n+i] = a[i];
    }
   for(int i = 1; i <= 2*n; i++) sum[i] = sum[i-1] + a[i];
   memset(dp1,INF,sizeof(dp1));
   memset(dp2,0,sizeof(dp2));
   for(int i = 0; i <= 2*n; i++) dp1[i][i] = dp2[i][i] = 0;
   for(int len = 2; len <= n; len++)
       for(int i = 1, j = len; j <= 2*n; i++,j++) // !j <= 2*n!
          for(int k = i; k < j; k++)
         {
           dp1[i][j] = min(dp1[i][j],dp1[i][k] + dp1[k+1][j] + sum[j]-sum[i-1]);
           dp2[i][j] = max(dp2[i][j],dp2[i][k] + dp2[k+1][j] + sum[j]-sum[i-1]);
         }

  int ans1 = INF,ans2 = 0;
  for(int i = 1; i <= n; i++)
  {
    ans1 = min(ans1,dp1[i][i+n-1]);
    ans2 = max(ans2,dp2[i][i+n-1]);
  }
  printf("%d\n%d\n",ans1,ans2);
  return 0;
}

```
