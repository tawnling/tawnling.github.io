---
title: Dynamic Programming Template
top: false
cover: false
toc: true
mathjax: false
date: 2019-09-27 00:11:52
password:
summary: 关于动态规划的一些模板代码
tags:
- template
- DP
- Algorithm
categories:
- Algorithm
---

## 最长上升子序列 nlogn
```cpp
// dp[k] 为长度为k时最小的 a[i]值;
//如果a[i] >= dp[j], 说明dp[j+1] = a[i];
//否则 二分查找dp{1-j} 中a[i] 所处的位置，并用a[i]取代。
dp[0] = -1;
int j = 0;
for(int i = 0; i < n; i++)
  if(a[i] >= dp[j]) dp[++j] = a[i];
  else {
    int idx = lower_bound(dp+1,dp+j+1,a[i]) - dp;
    dp[idx] = a[i];
  }
int ans = j;
```
