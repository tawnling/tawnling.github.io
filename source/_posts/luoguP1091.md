---
title: luoguP1091 合唱队形
top: false
cover: false
toc: true
mathjax: false
date: 2019-02-12 00:51:25
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

N位同学站成一排，音乐老师要请其中的(N−K)位同学出列，使得剩下的K位同学排成合唱队形。
合唱队形是指这样的一种队形：设K位同学从左到右依次编号为1,2,…,K，他们的身高分别为T1,T2,…,TK,则他们的身高满足T1<...<Ti>T{i+1}>…>TK(1≤i≤K)。
你的任务是，已知所有N位同学的身高，计算最少需要几位同学出列，可以使得剩下的同学排成合唱队形。

**【输入格式】**

共二行。  
第一行是一个整数N(2≤N≤100)，表示同学的总数。  
第二行有nn个整数，用空格分隔，第i个整数Ti(130≤Ti≤230)是第i位同学的身高(厘米)。  

**【输出格式】**

一个整数，最少需要几位同学出列

**【输入样例】**

8  
186 186 150 200 160 130 197 220


**【输出样例】**

4

**【说明】**

对于50％的数据，保证有n n≤20；

对于全部的数据，保证有n n≤100。

**【测试网站】**

[luoguP1091](https://www.luogu.org/problemnew/show/P1091)

## 二、题目分析

枚举中间那个最高的身高，然后分别从左从右向最高的身高求LIS,此时此种方案下出列的同学最少，然后取枚举的所有方案中的最优结果。

## 三、代码示例

```cpp
#include <bits/stdc++.h>

using namespace std;
const int maxn = 100+ 10;
const int INF = 0x3f3f3f3f;
int dp1[maxn],dp2[maxn],a[maxn];

int main()
{
  int n;
  scanf("%d",&n);
  for(int i = 1; i <= n; i++)
    scanf("%d",&a[i]);
  for(int i = 1; i <= n; i++)
    {
      dp1[i] = 1;
      for(int j = 1; j < i; j++)
        if(a[j] < a[i]) dp1[i] = max(dp1[i],dp1[j] + 1);
    }
  for(int i = n; i >= 1; i--)
   {
     dp2[i] = 1;
     for(int j = n; j > i; j--)
       if(a[j] < a[i]) dp2[i] = max(dp2[i],dp2[j] + 1);
   }
  int ans = INF;
  for(int i = 1; i <= n; i++)
     ans = min(ans,n-dp1[i]-dp2[i]+1);
  printf("%d\n",ans);
  return 0;
}

```
