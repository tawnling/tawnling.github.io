---
title: Codevs1288 埃及分数
top: false
cover: false
toc: true
mathjax: false
date: 2019-02-26 23:08:31
img:
password:
summary:
tags:
- Search
- Algorithm
categories:
- Algorithm
---
## 一、题目内容

**【题目描述】**

在古埃及，人们使用单位分数的和(形如1/a的, a是自然数)表示一切有理数。 如：2/3=1/2+1/6,但不允许2/3=1/3+1/3,因为加数中有相同的。 对于一个分数a/b,表示方法有很多种，但是哪种最好呢？ 首先，加数少的比加数多的好，其次，加数个数相同的，最小的分数越大越 好。 如： 19/45=1/3 + 1/12 + 1/180 19/45=1/3 + 1/15 + 1/45 19/45=1/3 + 1/18 + 1/30, 19/45=1/4 + 1/6 + 1/180 19/45=1/5 + 1/6 + 1/18. 最好的是最后一种，因为1/18比1/180,1/45,1/30,1/180都大。 给出a,b(0<a<b<1000),编程计算最好的表达方式

**【输入格式】**

a b

**【输出格式】**

若干个数，自小到大排列，依次是单位分数的分母。

**【输入样例】**

19 45  

**【输出样例】**

5 6 18  

**【测试网站】**

[codevs1288](http://codevs.cn/problem/1288/)

## 二、题目分析

题目意思为给定一个单位分数，拆分为几个单位分数之和，要求不能有相同的单位分数，且个数最少。当个数相同的，最小的分数最大的方案为最优。   
所以可以从小到大枚举个数，然后dfs搜索，即迭代加深搜索。  
需要注意的是，需要剪枝，    
1.枚举分母时由于受分数大小限制，所以分母有下限。
2.枚举分母是由于总个数确定maxd，当前已枚举的个数确定d,所以剩余个数确定maxd-d+1,
所以分母有上限，(maxd-d+1)x(1/i) > x/y  

## 三、代码示例

```cpp
#include <bits/stdc++.h>
using namespace std;
const int INF = 0x3f3f3f3f;
const int maxn = 2000;
typedef long long LL;

int maxd = 0;
LL ans[maxn],cur[maxn];

LL gcd(LL a, LL b) { return b == 0 ? a : gcd(b,a%b);}
int st(int x, int y) {for(int i = 2; ; i++ ) if(y <= x*i)return i;}

bool check(int d)
{
  if(ans[d] == -1) return true;
  if(ans[d] > cur[d]) return true;
  return false;
}

bool idfs(int d, int mind, LL x, LL y) //mind 最小的分母
{
  bool ok = false;
  if(d == maxd)
  {
    if(y%x != 0) return false;
    cur[d] = y/x;
    if(check(d))
    for(int i = 1; i <= d; i++)  ans[i] = cur[i];
    return true;
  }
  int s = max(mind,st(x,y));  //分母下界
  for(int i = s; ;i++)
   {
     if((maxd-d+1)*y <= x*i) break; //剪枝优化
     cur[d] = i;
     LL xx = x*i-y; LL yy = y*i; LL g = gcd(xx,yy);
     if(idfs(d+1,i+1,xx/g,yy/g)) ok = true;
   }
  return ok;
}

int main()
{
  LL x,y;
  scanf("%lld%lld",&x,&y);
  if(y%x == 0) {printf("%d\n",y/x);return 0;}
  int s = st(x,y);
  memset(ans,-1,sizeof(ans));
  while(++maxd)  if(idfs(1,s,x,y)) break;
  for(int i = 1; i <= maxd; i++)
    printf("%lld%c", ans[i], i == maxd? '\n' : ' ');
  return 0;
}

```
