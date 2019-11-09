---
title: luoguP2789 直线交点数
top: false
cover: false
toc: true
mathjax: false
date: 2019-02-09 09:48:44
img:
password:
summary:
tags:
- Rucursion
- Algorithm
categories:
- Algorithm
---

## 一、题目内容

**【题目描述】**

平面上有N条直线，且无三线共点，那么这些直线能有多少不同的交点数？

**【输入格式】**

一个正整数N

**【输出格式】**

一个整数表示方案总数

**【输入样例】**

4  

**【输出样例】**

5

**【说明】**

N<=25

**【测试网站】**

[luoguP2789](https://www.luogu.org/problemnew/show/P2789)



## 二、题目分析

题目求不同交点数的方案数，那么我们可以将所有情况的交点数都求出，然后查重统计答案。可以根据是否平行划分成不同的情况，比如，对于n个点，我们考虑，n条直线相互平行、n-1条直线相互平行、n-2条直线相互平行、、、，即n-r条直线相互平行，那个这r条相互平行的直线与剩下的直线的交点数 = r(n-r), 然后我们再递归到下一层来讨论n-r的情况，依次类推下去。请结合代码理解！


## 三、代码示例

```cpp
#include <bits/stdc++.h>

using namespace std;
const int maxn = 3000;

int n,ans = 0;
bool vis[1000];

void fun(int x, int y)
{
  if(x == 0) {if(!vis[y]) ans++; vis[y] = true; return ;}
  for(int i = x; i >= 1; i--)  fun(x-i,i*(x-i) + y);
}

int main()
{
  cin >> n;
  memset(vis,false,sizeof(vis));
  fun(n,0);
  cout << ans << endl;
}


```
