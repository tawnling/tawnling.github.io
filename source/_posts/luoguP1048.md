---
title: luoguP1048 采药
top: false
cover: false
toc: true
mathjax: false
date: 2019-02-04 09:08:19
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

辰辰是个天资聪颖的孩子，他的梦想是成为世界上最伟大的医师。为此，他想拜附近最有威望的医师为师。医师为了判断他的资质，给他出了一个难题。医师把他带到一个到处都是草药的山洞里对他说：“孩子，这个山洞里有一些不同的草药，采每一株都需要一些时间，每一株也有它自身的价值。我会给你一段时间，在这段时间里，你可以采到一些草药。如果你是一个聪明的孩子，你应该可以让采到的草药的总价值最大。”

如果你是辰辰，你能完成这个任务吗？

**【输入格式】**

第一行有2个整数 T (1 ≤ T ≤ 1000) 和 M ( 1 ≤ M ≤ 100)，用一个空格隔开，T代表总共能够用来采药的时间，MM代表山洞里的草药的数目。接下来的M行每行包括两个在1到100之间（包括1和100）的整数，分别表示采摘某株草药的时间和这株草药的价值。

**【输出格式】**

1个整数，表示在规定的时间内可以采到的草药的最大总价值。

**【输入样例】**

70 3  
71 100  
69 1  
1 2  


**【输出样例】**

3

**【说明】**

对于30%的数据，M ≤ 10；

对于全部的数据，M ≤ 100。

NOIP2005普及组第三题

**【测试网站】**

[luoguP1048](https://www.luogu.org/problemnew/show/P1048)



## 二、题目分析

01背包模板题目，采药时间作为限制条件，等同于背包问题的体积，药品价值作为价值衡量，然后利用套用01背包模板解题。

## 三、代码示例

```cpp
#include <bits/stdc++.h>

using namespace std;
const int maxn = 100 + 10;
const int maxc = 1000 + 10;

int dp[maxc] = {0};
int v[maxn],w[maxn];
int main()
{
    int T,M;
  scanf("%d%d",&T,&M);
    for(int i = 1; i <= M; i++)
     scanf("%d%d",&v[i],&w[i]);
    for(int i = 1; i <= M; i++)
      for(int j = T; j >= v[i]; j--)
           dp[j] = max(dp[j],dp[j-v[i]] + w[i]);
    printf("%d\n",dp[T]);
    return 0;
}


```
