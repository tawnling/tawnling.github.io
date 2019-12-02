---
title: luoguP1140 相似基因
top: false
cover: false
toc: true
mathjax: false
date: 2019-02-26 08:13:35
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

大家都知道，基因可以看作一个碱基对序列。它包含了44种核苷酸，简记作A,C,G,TA,C,G,T。生物学家正致力于寻找人类基因的功能，以利用于诊断疾病和发明药物。

在一个人类基因工作组的任务中，生物学家研究的是：两个基因的相似程度。因为这个研究对疾病的治疗有着非同寻常的作用

两个基因的相似度的计算方法如下：  

对于两个已知基因，例如AGTGATGAGTGATG和GTTAGGTTAG，将它们的碱基互相对应。当然，中间可以加入一些空碱基-，例如：

| A | G | T | G | A | T | - | G |
|-|-|-|-|-|-|-|-|
| - | G | T | - | - | T | A | G |

这样,两个基因之间的相似度就可以用碱基之间相似度的总和来描述，碱基之间的相似度如下表所示：

||A|C|G|T|-|
|-||-|-|-|-|-|
|A|5|-1|-2|-1|-3|
|C|-1|5|-3|-2|-4|
|G|-2|-3|5|-2|-2|
|T|-1|-2|-2|5|-1|
|-|-3|-4|-2|-1|*|
那么相似度就是：
(-3)+5+5+(-2)+(-3)+5+(-3)+5=9(−3)+5+5+(−2)+(−3)+5+(−3)+5=9。
因为两个基因的对应方法不唯一，例如又有：

| A | G | T | G | A | T | G |
|-|-|-|-|-|-|-|-|
| - | G | T | T | A | - | G |

相似度为：(-3)+5+5+(-2)+5+(-1)+5=14(−3)+5+5+(−2)+5+(−1)+5=14。规定两个基因的相似度为所有对应方法中，相似度最大的那个。

**【输入格式】**

共两行。每行首先是一个整数，表示基因的长度；隔一个空格后是一个基因序列，序列中只含A,C,G,TA,C,G,T四个字母。1 \le 1≤序列的长度 \le 100≤100。

**【输出格式】**

仅一行，即输入基因的相似度。

**【输入样例】**

7 AGTGATG  
5 GTTAG

**【输出样例】**

14

**【测试网站】**

[luoguP1140](https://www.luogu.org/problemnew/show/P1140)

## 二、题目分析

dp[i][j] 表示第一个基因序列前i个和第二个基因序列前j个进行配对时的最大相似度。

dp[i][j] = max(dp[i][j],dp[i-1][j-1] + mp[a[i]][b[j]])
dp[i][j] = max(dp[i][j],dp[i-1][j] + mp[a[i]]['-'])
dp[i][j] = max(dp[i][j],dp[i][j-1] + mp['-'][b[j]])

初始化:
所有的dp[i][0]为mp[a[i]['-']]
所有的dp[0][i]为mp['-'][b[i]]

## 三、代码示例

```cpp
#include <bits/stdc++.h>

using namespace std;
const int maxn = 100 + 10;
const int INF = 0x3f3f3f3f;
int dp[maxn][maxn] = {0};
string sa,sb;
int a[maxn],b[maxn];
int n,m;
int mp[5][5] =
{
  5, -1, -2, -1, -3,
  -1, 5, -3, -2, -4,
  -2, -3, 5, -2, -2,
  -1, -2, -2, 5, -1,
  -3, -4, -2, -1, 0
};
int turn(char c)
{
  switch (c) {
    case 'A': return 0;
    case 'C': return 1;
    case 'G': return 2;
    case 'T': return 3;
  }
}
int main()
{
  scanf("%d",&n); cin >> sa;
  scanf("%d",&m); cin >> sb;
  for(int i = 0; i < n; i++)  a[i+1] = turn(sa[i]);
  for(int i = 0; i < m; i++)  b[i+1] = turn(sb[i]);
  dp[0][0] = 0;
  //初始化时需要注意
  for(int i = 1; i <= n; i++)  dp[i][0] = dp[i-1][0] + mp[a[i]][4];
  for(int i = 1; i <= m; i++)  dp[0][i] = dp[0][i-1] + mp[4][b[i]];
  for(int i = 1; i <= n; i++)
    for(int j = 1; j <= m; j++)
      {
        dp[i][j] =  dp[i-1][j-1] + mp[a[i]][b[j]];
        dp[i][j] = max(dp[i][j], dp[i-1][j] + mp[a[i]][4]);
        dp[i][j] = max(dp[i][j], dp[i][j-1] + mp[4][b[j]]);
      //  cout << i << " " << j << " " << dp[i][j] << endl;
      }
  printf("%d\n",dp[n][m]);
  return 0;
}

```
