---
title: luoguP1049 装箱问题
top: false
cover: false
toc: true
mathjax: false
date: 2019-02-04 09:08:26
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

有一个箱子容量为V（正整数，0 ≤ V ≤ 20000），同时有n个物品 0<n≤30，每个物品有一个体积（正整数）。

要求n个物品中，任取若干个装入箱内，使箱子的剩余空间为最小。

**【输入格式】**

1个整数，表示箱子容量

1个整数，表示有n个物品

接下来n行，分别表示这n个物品的各自体积  

**【输出格式】**

1个整数，表示箱子剩余空间。

**【输入样例】**

24  
6  
8  
3  
12  
7  
9  
7  


**【输出样例】**

0

**【说明】**

NOIp2001普及组 第4题

**【测试网站】**

[luoguP1049](https://www.luogu.org/problemnew/show/P1049)



## 二、题目分析

01背包模板题目，体积作为限制条件，等同于背包问题的体积，体积也同时作为价值衡量，然后利用套用01背包模板解题，最后答案为剩余空间为箱子体积V - dp[V]。

## 三、代码示例

```cpp
#include <bits/stdc++.h>

using namespace std;
const int maxc = 20000 + 10;
const int maxn = 30 + 10;

int v[maxn];
int dp[maxc] = {0};

int main()
{
  int V,n;
  scanf("%d%d",&V,&n);
  for(int i = 1; i <= n; i++) scanf("%d",&v[i]);
  for(int i = 1; i <= n; i++)
    for(int j = V; j >= v[i]; j--)
      dp[j] = max(dp[j],dp[j-v[i]] + v[i]);
  printf("%d\n",V-dp[V]);
  return 0;
}

```
