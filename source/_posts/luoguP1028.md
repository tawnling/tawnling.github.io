---
title: luoguP1028 数的计算
top: false
cover: false
toc: true
mathjax: false
date: 2018-12-17 16:55:30
img:
password:
summary:
tags:
- Recursion
- Algorithm
categories:
- Algorithm
---

## 一、题目内容

**【题目描述】**

我们要求找出具有下列性质数的个数(包含输入的自然数 n ):

先输入一个自然数 n (n ≤ 1000),然后对此自然数按照如下方法进行处理:

    1.不作任何处理;

    2.在它的左边加上一个自然数,但该自然数不能超过原数的一半;

    3.加上数后,继续按此规则进行处理,直到不能再加自然数为止.


**【输入格式】**

1个自然数 n (n ≤ 1000)

**【输出格式】**

1个整数，表示具有该性质数的个数。

**【输入样例】**

6

**【输出样例】**

6

**【说明】**

满足条件的数为

6，16，26，126，36，136

**【测试网站】**

[luoguP1028](https://www.luogu.org/problemnew/show/P1028)



## 二、题目分析

题目给出一个n, 然后根据题目给的操作我们可以得到一些数，题目求总共得到的数的个数。所以我们可以递归的求出所有得到的数。为了不重复进行递归操作，我们用num[i] 数组来记录从 i 递归下去可以得到的数的个数，包括它本身。num数组开始时全部初始化为-1，num[i] ==  -1 表示 i 还未被递归求过。num[i] != -1 则说明 i 已经被递归过了，则不需要递归，直接返回num[i] 即可。

## 三、代码示例
```cpp
#include <iostream>
#include <cstring> // memset函数的头文件

using namespace std;
const int maxn = 1000 + 10;

int num[maxn];

int dfs(int x)
{
  //num[x] != -1 则说明 x 已经被递归过了，则不需要递归，直接返回num[x] 即可。
  if(num[x] != -1) return num[x];
  //num[x] ==  -1 表示 x 还未被递归求过
  num[x] = 1;//代表自己的 1
  // 搜索小于等于自己一半的数，统计通过这些数可以得到的数的个数
  for(int i = 1; i <= x/2; i++)  num[x] += dfs(i);
  return num[x]; //统计完所有的把num[x]返回到上一层
}

int main()
{
  int n,ans = 0;
  memset(num,-1,sizeof(num)); // memset函数 ： 将num数组中的所有元素赋值为-1
  cin >> n; // 输入n
  dfs(n);  //递归计算从n出发能得到的数的总数
  cout << num[n] << endl;
  return 0;
}

```
