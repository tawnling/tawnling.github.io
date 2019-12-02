---
title: nowcoder317C 小a与星际探索
top: false
cover: false
toc: true
mathjax: false
date: 2019-01-23 18:11:41
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

小a正在玩一款星际探索游戏，小a需要驾驶着飞船从1号星球出发前往n号星球。其中每个星球有一个能量指数p。星球i能到达星球j当且仅当 pi > pj 。同时小a的飞船还有一个耐久度 t，初始时为 1号点的能量指数，若小a前往星球 j，那么飞船的耐久度会变为 t⊕pj (即t异或pj，关于其定义请自行百度) 小a想知道到达n号星球时耐久度最大为多少 注意：对于每个位置来说，从它出发可以到达的位置仅与两者的p有关，与下标无关。

**【输入格式】**

第一行一个整数n，表示星球数接下来一行有n个整数，第i个整数表示pi。

**【输出格式】**

一个整数表示到达n号星球时最大的耐久度
若不能到达n号星球或到达时的最大耐久度为0则输出−1

**【输入样例】**

3  
457 456 23

**【输出样例】**

478

**【说明】**

小a有两种方法到达3号星球
第一种：1→2→3，最终耐久度为457⊕456⊕23=22
第二种：1→3，最终耐久度为 457⊕3=478

1⩽n,∀pi⩽3000

**【测试网站】**

[nowcoder317C](https://ac.nowcoder.com/acm/contest/317/C)



## 二、题目分析

到达性dp问题，与极值dp略作区分(自己瞎编的haha)
首先可以按照pi从大到排序，将1和n之间的p取出来并去重，然后直接在新序列上做背包，用dp[i][j]表示到达第i个位置，当前耐久度为j是否可行。转移的时候分两种情况讨论：
1. 到达该点
2. 不到达该点
对n位置特殊判断一下，最后枚举n所在位置dp数组的第二维，判断一下即可
注意一个小细节：dp数组的第二维不能只开到3000要开到4096
时间复杂度：O(np)


## 三、代码示例

```cpp
#include <bits/stdc++.h>

using namespace std;
typedef long long LL;
typedef pair <LL,int> P;
const int INF = 0x3f3f3f3f;
const int maxn = 3000 + 10;
const int maxm = 4096+ 10;

bool dp[maxn][maxm];
int p[maxn];
int main()
{
  int n,tot = 1;
  scanf("%d",&n);
  for(int i = 1; i <= n; i++)  scanf("%d",&p[i]);
  for(int i = 2; i < n; i++)
  if(p[i] < p[1] && p[n] < p[i]) p[++tot] = p[i];
  p[++tot] = p[n];
  sort(p+1,p+tot+1);
  n = tot;
  tot = 1;
  for(int i = 2; i <= n; i++)
  if(p[i] != p[i-1]) p[++tot] = p[i];
  int m = 4096;
  memset(dp,false,sizeof(dp));
  dp[1][p[1]] = true;
  for(int i = 2; i <= tot; i++)
   for(int j = m; j >= 0; j--)
      {
        if(i != tot)
        dp[i][j] = dp[i-1][j^p[i]] || dp[i-1][j];
        else {
          dp[i][j] = dp[i][j] || dp[i-1][j^p[i]];
          if(dp[i][j]) {printf("%d\n",j); return 0;}
      }
    }
  printf("-1\n");
  return 0;
 }

```
