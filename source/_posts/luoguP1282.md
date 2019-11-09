---
title: luoguP1282 多米诺骨牌
top: false
cover: false
toc: true
mathjax: false
date: 2019-02-25 23:57:43
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

多米诺骨牌有上下2个方块组成，每个方块中有1~6个点。现有排成行的

上方块中点数之和记为S1，下方块中点数之和记为S2，它们的差为|S1-S2|。例如在图8-1中，S1=6+1+1+1=9，S2=1+5+3+2=11，|S1-S2|=2。每个多米诺骨牌可以旋转180°，使得上下两个方块互换位置。 编程用最少的旋转次数使多米诺骨牌上下2行点数之差达到最小。

![你想输入的替代文字](https://cdn.luogu.org/upload/pic/91.png)

对于图中的例子，只要将最后一个多米诺骨牌旋转180°，可使上下2行点数之差为0。

**【输入格式】**

输入文件的第一行是一个正整数n(1≤n≤1000)，表示多米诺骨牌数。接下来的n行表示n个多米诺骨牌的点数。每行有两个用空格隔开的正整数，表示多米诺骨牌上下方块中的点数a和b，且1≤a，b≤6。

**【输出格式】**

输出文件仅一行，包含一个整数。表示求得的最小旋转次数。

**【输入样例】**

4  
6 1  
1 5  
1 3  
1 2  

**【输出样例】**

1

**【测试网站】**

[luoguP1282](https://www.luogu.org/problemnew/show/P1282)

## 二、题目分析

dp[i][j] 表示前i个第一行达到和为j时旋转次数最小值
dp[i][j] = min(dp[i-1][j],dp[i-1][j-a[i]]
(1 <= i <= n, 0 <= j <= 6*n)
最后在枚举j，求其对应的和的差值，统计差值最小的情况下的最小旋转次数，即答案

## 三、代码示例

```cpp
#include <bits/stdc++.h>

using namespace std;
const int maxn = 1000 + 10;
const int INF = 0x3f3f3f3f;

int n,s = 0;
int a[maxn],b[maxn],dp[maxn][maxn*6+10];

int main()
{
  scanf("%d",&n);
  for(int i = 1; i <= n; i++)
  {
    scanf("%d%d",&a[i],&b[i]);
    s += a[i] + b[i];
  }
  memset(dp,INF,sizeof(dp));
  dp[1][a[1]] = 0; dp[1][b[1]] = 1;
  for(int i = 2; i <= n; i++)
   for(int j = 0; j <= 6*n; j++)
     {
       if(j >= a[i]) dp[i][j] = min(dp[i][j],dp[i-1][j-a[i]]);
       if(j >= b[i]) dp[i][j] = min(dp[i][j],dp[i-1][j-b[i]] + 1);
     }
  int ans = INF,anst = INF;
  for(int j = 0; j <= s; j++)
    if(dp[n][j] < INF)
     if(anst > abs(s-j-j))
      anst = abs(s-j-j), ans = dp[n][j];
    else if(anst == abs(s-j-j)) ans = min(ans,dp[n][j]);

  printf("%d\n",ans);
  return 0;
}

```
