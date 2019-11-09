---
title: luoguP2758 编辑距离
top: false
cover: false
toc: true
mathjax: false
date: 2019-02-26 18:25:18
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

设A和B是两个字符串。我们要用最少的字符操作次数，将字符串A转换为字符串B。这里所说的字符操作共有三种：

1、删除一个字符；

2、插入一个字符；

3、将一个字符改为另一个字符；

！皆为小写字母！

**【输入格式】**

第一行为字符串A；第二行为字符串B；字符串A和B的长度均小于2000。


**【输出格式】**

只有一个正整数，为最少字符操作次数。

**【输入样例】**

sfdqxbw  
gfdgw  

**【输出样例】**

4  

**【测试网站】**

[luoguP2758](https://www.luogu.org/problemnew/show/luoguP2758)

## 二、题目分析

dp[i][j] 表示第一行的前i个字母和第二行的前j个字母进行匹配
通过增删替换等操作有如下动态转移方程  
dp[i][j] = min(dp[i-1][j]+1,dp[i][j-1]+1,dp[i-1][j-1]+ (s[i] != s[j])

## 三、代码示例

```cpp
#include <iostream>
#include <cstdio>
using namespace std;
const int maxn = 2000 + 10;
const int INF = 0x3f3f3f3f;

//dp[i][j] = min(dp[i-1][j]+1,dp[i][j-1]+1,dp[i-1][j-1]+ (s[i] != s[j])

int dp[maxn][maxn] = {0};

int main()
{
  string s1,s2;
  cin >> s1 >> s2;
  int n = s1.size();
  int m = s2.size();
  for(int i = 1; i <= m; i++) dp[0][i] = i;
  for(int i = 1; i <= n; i++) dp[i][0] = i;
  for(int i = 1; i <= n; i++)
   for(int j = 1; j <= m; j++)
     {
       dp[i][j] = min(dp[i-1][j]+1,dp[i][j-1]+1);
       dp[i][j] = min(dp[i][j],dp[i-1][j-1] + (s1[i-1] != s2[j-1]));
       //cout << i << " " << j << " " << dp[i][j] << endl;
     }
  printf("%d\n",dp[n][m]);
  return 0;
}

```
