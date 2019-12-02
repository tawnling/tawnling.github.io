---
title: luoguP1280 尼克的任务
top: false
cover: false
toc: true
mathjax: false
date: 2019-02-25 23:57:18
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

尼克每天上班之前都连接上英特网，接收他的上司发来的邮件，这些邮件包含了尼克主管的部门当天要完成的全部任务，每个任务由一个开始时刻与一个持续时间构成。

尼克的一个工作日为N分钟，从第一分钟开始到第N分钟结束。当尼克到达单位后他就开始干活。如果在同一时刻有多个任务需要完成，尼克可以任选其中的一个来做，而其余的则由他的同事完成，反之如果只有一个任务，则该任务必需由尼克去完成，假如某些任务开始时刻尼克正在工作，则这些任务也由尼克的同事完成。如果某任务于第P分钟开始，持续时间为T分钟，则该任务将在第P+T-1分钟结束。

写一个程序计算尼克应该如何选取任务，才能获得最大的空暇时间。


**【输入格式】**

输入数据第一行含两个用空格隔开的整数N和K(1≤N≤10000，1≤K≤10000)，N表示尼克的工作时间，单位为分钟，K表示任务总数。

接下来共有K行，每一行有两个用空格隔开的整数P和T，表示该任务从第P分钟开始，持续时间为T分钟，其中1≤P≤N，1≤P+T-1≤N。

**【输出格式】**

输出文件仅一行，包含一个整数，表示尼克可能获得的最大空暇时间。

**【输入样例】**

15 6  
1 2  
1 6  
4 11  
8 5  
8 1   
11 5    

**【输出样例】**

4  

**【测试网站】**

[luoguP1280](https://www.luogu.org/problemnew/show/P1280)


## 二、题目分析

设dp[i] 表示当时间为i时，尼克在之后能获得的最大空暇。
从后往前进行dp
如果该时刻没有任务，dp[i] = dp[i+1] + 1
如果该时刻有任务，  dp[i] = max(dp[i],dp[i+T])
最终答案为dp[1],初始时全为0。

## 三、代码示例

```cpp
#include <bits/stdc++.h>

using namespace std;
const int maxn = 10000 + 10;
typedef pair<int,int> P;

int n,k;
int p[maxn],t[maxn],dp[maxn] = {0};
int a[maxn];

bool cmp(int x, int y){  return p[x] > p[y];}

int main()
{
  scanf("%d%d",&n,&k);
  for(int i = 1; i <= k; i++)
  {
    scanf("%d%d",&p[i], &t[i]);
    a[i] = i;
  }
  sort(a+1,a+k+1,cmp);
  //for(int i = 1; i <= k; i++) cout << a[i] << endl;
  for(int i = n,j = 1; i >= 1; i--)
  {
    //cout << i <<' ' << p[a[j]] << endl;
    if(i != p[a[j]]) {dp[i] = dp[i+1] + 1;continue;}
    while(i == p[a[j]])
      {
        dp[i] = max(dp[i],dp[i+t[a[j]]]);
        j++;
      }
  //  cout << dp[i] << " * " << endl;
  }
  printf("%d\n",dp[1]);
  return 0;
}

```
