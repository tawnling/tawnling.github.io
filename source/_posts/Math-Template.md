---
title: Math Template
top: false
cover: false
toc: true
mathjax: false
date: 2019-09-27 01:37:04
password:
summary: 关于数学的一些代码模板
tags:
- template
- Math
- Algorithm
categories:
- Algorithm
---
# 数学

## 行列式求值

+ 高斯消元

```cpp
#define type long long
type det(type a[][maxn],int n)
{
  type ret=1;
  for(int i=1;i<=n;i++)
  {
    for(int j=i+1;j<=n;j++)
    {
      while(a[j][i])
      {
        type t=a[i][i]/a[j][i];
        for(int k=i;k<=n;k++)
            a[i][k]=a[i][k]-a[j][k]*t;

        for(int k=i;k<=n;k++)
            swap(a[i][k],a[j][k]);
        ret=-ret;
      }
    }
    if(a[i][i]==0)  return 0;
    ret=ret*a[i][i];
  }
  if(ret<0)   ret=-ret;
  return ret;
}
