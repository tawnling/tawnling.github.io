---
title: luoguP1020 导弹拦截
top: false
cover: false
toc: true
mathjax: false
date: 2019-02-11 21:40:08
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

某国为了防御敌国的导弹袭击，发展出一种导弹拦截系统。但是这种导弹拦截系统有一个缺陷：虽然它的第一发炮弹能够到达任意的高度，但是以后每一发炮弹都不能高于前一发的高度。某天，雷达捕捉到敌国的导弹来袭。由于该系统还在试用阶段，所以只有一套系统，因此有可能不能拦截所有的导弹。

输入导弹依次飞来的高度（雷达给出的高度数据是≤50000的正整数），计算这套系统最多能拦截多少导弹，如果要拦截所有导弹最少要配备多少套这种导弹拦截系统。

**【输入格式】**

1行，若干个整数（个数 ≤100000）

**【输出格式】**

2行，每行一个整数，第一个数字表示这套系统最多能拦截多少导弹，第二个数字表示如果要拦截所有导弹最少要配备多少套这种导弹拦截系统

**【输入样例】**

389 207 155 300 299 170 158 65

**【输出样例】**

6  
2

**【说明】**

为了让大家更好地测试n方算法，本题开启spj，n方100分，nlogn200分

每点两问，按问给分

**【测试网站】**

[luoguP1020](https://www.luogu.org/problemnew/show/P1020)

## 二、题目分析

1.第一问求最长不上升子序列。反过来即最长不下降子序列，有n^2 的做法，还有nlogn的做法。
n^2做法：dp[i]表示前i个数最长的满足要求的序列长度。   
nlogn 做法： dp[j] 表示长度为j时，最小的数值。 对于每一个a[i], 二分查找其应该放入dp数组中的哪个位置。  
2.第二问求最少可划分为多少个不上升子序列。
应用dilworth定理：  设(X,≤) 是一个有限偏序集，并令m是反链的最大的大小。则X可以被划分成m个但不能再少的链  
最少链划分 = 最长反链长度
所以最少划分数 = LIS  
[dilworth定理的讲解与证明](https://www.cnblogs.com/flipped/p/5009943.html) 《== 请点击
所以只需要用前面提到的nlogn的算法求LIS即可。  

## 三、代码示例

```cpp
#include <bits/stdc++.h>

using namespace std;
const int maxn = 1e5+ 10;
const int INF = 0x3f3f3f3f;
int dp[maxn],a[maxn];

int main()
{
  int n = 0,ans = 0;
  while(~scanf("%d",&a[n])) n++;
  dp[0] = INF;
  for(int i = n-1; i >= 0; i--)
   {
     int j = upper_bound(dp,dp+ans,a[i]) - dp;
     dp[j] = a[i];
     ans = max(ans,j+1);
   }
  cout << ans << endl;
  ans = 0;
  dp[0] = INF;
  for(int i = 0; i < n; i++)
    {
      int j = lower_bound(dp,dp+ans,a[i]) - dp;
      dp[j] = a[i];
      ans = max(ans,j+1);
    }
   cout << ans << endl;
  return 0;
}

```
