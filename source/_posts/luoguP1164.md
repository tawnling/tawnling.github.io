---
title: luoguP1164 小A点菜
top: false
cover: false
toc: true
mathjax: false
date: 2019-02-04 09:06:41
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

uim神犇拿到了uoi的ra（镭牌）后，立刻拉着基友小A到了一家……餐馆，很低端的那种。
uim指着墙上的价目表（太低级了没有菜单），说：“随便点”。

不过uim由于买了一些辅（e）辅（ro）书，口袋里只剩M元(M≤10000)。
餐馆虽低端，但是菜品种类不少，有 N 种 (N≤100)，第i种卖 a[i]
元)(a[i] ≤ 1000)。
由于是很低端的餐馆，所以每种菜只有一份。
小A奉行“不把钱吃光不罢休”，所以他点单一定刚好吧uim身上所有钱花完。他想知道有多少种点菜方法。

由于小A肚子太饿，所以最多只能等待11秒。

**【输入格式】**

第一行是两个数字，表示NN和MM。

第二行起NN个正数a[i], 可以有相同的数字，每个数字均在1000以内。

**【输出格式】**

一个正整数，表示点菜方案数，保证答案的范围在int之内。

**【输入样例】**

4 4  
1 1 2 2


**【输出样例】**

3

**【说明】**

NOIP 2006 普及组 第二题

**【测试网站】**

[luoguP1164](https://www.luogu.org/problemnew/show/P1164)

## 二、题目分析

01背包模板题目。第二种01背包问题类型，设dp[i][j], 为考虑前i件物品用光j元的方案数。   
（1）if(j==第i道菜的价格) dp[i][j] = dp[i-1][j]+1;  
（2）if(j>第i道菜的价格)  dp[i][j] = dp[i-1][j]+dp[i-1][j-第i道菜的价格];   
（3）if(j<第i道菜的价格) dp[i][j] = dp[i-1][j];

也可用滚动数组优化，初始化时dp[] = {0}, dp[0] = 1;    
动态转移方程为  dp[j] += dp[j-a[i]]; (j: m : a[i])

## 三、代码示例

```cpp
#include <bits/stdc++.h>

using namespace std;

const int maxm = 10000 + 10;
const int maxn = 100 + 10;

int a[maxn];
int dp[maxm] = {0};

int main()
{
  int n,m;
    dp[0] = 1;
    scanf("%d%d",&n,&m);
    for(int i = 1; i <= n; i++)
    {
        scanf("%d",&a[i]);
      for(int j = m; j >= a[i]; j--)
         dp[j] += dp[j-a[i]];
     }
    printf("%d\n",dp[m]);
     return 0;
}

```
