---
title: luoguP1064 金明的预算方案
top: false
cover: false
toc: true
mathjax: false
date: 2019-02-04 09:06:19
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

金明今天很开心，家里购置的新房就要领钥匙了，新房里有一间他自己专用的很宽敞的房间。更让他高兴的是，妈妈昨天对他说：“你的房间需要购买哪些物品，怎么布置，你说了算，只要不超过N元钱就行”。今天一早金明就开始做预算，他把想买的物品分为两类：主件与附件，附件是从属于某个主件的，下表就是一些主件与附件的例子：

主件 附件

电脑 打印机，扫描仪

书柜 图书

书桌 台灯，文具

工作椅 无

如果要买归类为附件的物品，必须先买该附件所属的主件。每个主件可以有0个、1个或2个附件。附件不再有从属于自己的附件。
但是他想买的东西太多了，肯定会超过妈妈限定的N元。于是，他把每件物品规定了一个重要度，分为5等：用整数1-5表示，第5等最重要。他还从因特网上查到了每件物品的价格（都是整数元）。他希望在不超过N元（可以等于N元）的前提下，使每件物品的价格与重要度的乘积的总和最大。
设第j件物品的价格为v[j], 重要度为w[j], 共选中了k件物品，编号一次为j1,j2,...jk,则所求总和为：v[j1] * w[j1] + v[j2] * w[j2] + ... + v[jk] * w[jk]。
请你帮助金明设计一个满足要求的购物单。

**【输入格式】**

第11行，为两个正整数，用一个空格隔开：

N m （其中 N(<32000) 表示总钱数，m(<60)为希望购买物品的个数。） 从第2行到第m+1行，第j行给出了编号为j−1的物品的基本数据，每行有3个非负整数 vpq （其中vv表示该物品的价格(v<10000)，p表示该物品的重要度（1-5），q表示该物品是主件还是附件。如果q=0，表示该物品为主件，如果q>0，表示该物品为附件，q是所属主件的编号）

**【输出格式】**

一个正整数，为不超过总钱数的物品的价格与重要度乘积的总和的最大值（<200000）。

**【输入样例】**

1000 5      
800 2 0  
400 5 1  
300 5 1  
400 3 0  
500 2 0     



**【输出样例】**


2200
**【说明】**

NOIP 2006 提高组 第二题

**【测试网站】**

[luoguP1064](https://www.luogu.org/problemnew/show/P1064)


## 二、题目分析

01背包模板题目，价格作为限制条件，等同于背包问题的体积，价格和重要度的乘积作为价值衡量，然后利用套用01背包模板解题。
然后这题是P1060的升级版，要分类讨论一下。
1.不取该主件
2.只取该主件
3.取主件和附件1
4.取主件和附件2
5.取主件和附件1、2


## 三、代码示例

```cpp
#include <bits/stdc++.h>

using namespace std;
const int maxc = 32000 + 10;
const int maxn = 100 + 10;
typedef pair<int,int> P;

int c,m,u,p,q,tot = 0;
map<int,int> mp;
vector<P> vs[maxn];
int dp[maxc];
int main()
{
  scanf("%d%d",&c,&m);
  for(int i = 1; i <= m; i++)
   {
     scanf("%d%d%d",&u,&p,&q);
     if(q == 0)
       {
         mp[i] = ++tot;
         vs[tot].push_back(P(u,u*p));
       }
    else vs[mp[q]].push_back(P(u,u*p));
   }
  for(int i = 1; i <= tot; i++)
    for(int j = c; j >= vs[i][0].first; j--)
      {
        dp[j] = max(dp[j],dp[j - vs[i][0].first] + vs[i][0].second);
        if(vs[i].size() >= 2 && j - vs[i][0].first - vs[i][1].first >= 0) dp[j] = max(dp[j],dp[j - vs[i][0].first - vs[i][1].first] + vs[i][0].second + vs[i][1].second);
        if(vs[i].size() >= 3 && j - vs[i][0].first - vs[i][2].first >= 0) dp[j] = max(dp[j],dp[j - vs[i][0].first - vs[i][2].first] + vs[i][0].second + vs[i][2].second);
        if(vs[i].size() >= 3 && j - vs[i][0].first - vs[i][1].first - vs[i][2].first >= 0) dp[j] = max(dp[j],dp[j - vs[i][0].first - vs[i][1].first - vs[i][2].first] + vs[i][0].second + vs[i][1].second + vs[i][2].second);
      }
   printf("%d\n",dp[c]);
   return 0;
}

```
