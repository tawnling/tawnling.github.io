---
title: luoguP1122 最大子树和
top: false
cover: false
toc: true
mathjax: false
date: 2019-02-26 15:41:07
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

小明对数学饱有兴趣，并且是个勤奋好学的学生，总是在课后留在教室向老师请教一些问题。一天他早晨骑车去上课，路上见到一个老伯正在修剪花花草草，顿时想到了一个有关修剪花卉的问题。于是当日课后，小明就向老师提出了这个问题：

一株奇怪的花卉，上面共连有N N朵花，共有N-1N−1条枝干将花儿连在一起，并且未修剪时每朵花都不是孤立的。每朵花都有一个“美丽指数”，该数越大说明这朵花越漂亮，也有“美丽指数”为负数的，说明这朵花看着都让人恶心。所谓“修剪”，意为：去掉其中的一条枝条，这样一株花就成了两株，扔掉其中一株。经过一系列“修剪“之后，还剩下最后一株花（也可能是一朵）。老师的任务就是：通过一系列“修剪”（也可以什么“修剪”都不进行），使剩下的那株（那朵）花卉上所有花朵的“美丽指数”之和最大。

老师想了一会儿，给出了正解。小明见问题被轻易攻破，相当不爽，于是又拿来问你。

**【输入格式】**

第一行一个整数N(1 ≤ N ≤ 16000)N(1≤N≤16000)。表示原始的那株花卉上共N N朵花。

第二行有N N个整数，第II个整数表示第II朵花的美丽指数。

接下来N-1N−1行每行两个整数a,ba,b，表示存在一条连接第aa 朵花和第bb朵花的枝条。

**【输出格式】**

一个数，表示一系列“修剪”之后所能得到的“美丽指数”之和的最大值。保证绝对值不超过21474836472147483647。

**【输入样例】**

7  
-1 -1 -1 1 1 1 0  
1 4  
2 5  
3 6  
4 7  
5 7  
6 7  

**【输出样例】**

3

**【说明】**

【数据规模与约定】

对于60%的数据，有N≤1000；

对于100%的数据，有N≤16000。

**【测试网站】**

[luoguP11122](https://www.luogu.org/problemnew/show/P1122)

## 二、题目分析

简单的树形DP,dp[i] += max(0,dp[j]), j 为 i 的子节点

## 三、代码示例

```cpp
#include <bits/stdc++.h>
using namespace std;
const int maxn = 16000 + 10;
const int INF = 0x3f3f3f3f;
int n,a[maxn],f[maxn] = {0};
vector <int> G[maxn];
bool vis[maxn];

void dfs(int x)
{
  f[x] = a[x];
  for(int i = 0; i < G[x].size(); i++)
  {
    int u = G[x][i];
    if(!vis[u]) {
      vis[u] = true;
      dfs(u);
      f[x] += max(0,f[u]);
    }
  }
}

int main()
{
    scanf("%d",&n);
    for(int i = 1; i <= n; i++) scanf("%d",&a[i]);
    for(int i = 1; i < n; i++)
    {
      int u,v;
      scanf("%d%d",&u,&v);
      G[u].push_back(v);
      G[v].push_back(u);
    }
    memset(vis,false, sizeof(vis));
    vis[1] = true;
    dfs(1);
    int ans = -INF;
    for(int i = 1; i <= n; i++)
        ans = max(ans,f[i]);

    printf("%d\n",ans);
    return 0;
}

```
