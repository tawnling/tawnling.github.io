---
title: Binary Index Tree
top: true
cover: false
toc: true
mathjax: true
img: http://img3.imgtn.bdimg.com/it/u=2387911928,2700617596&fm=214&gp=0.jpg
date: 2019-10-03 19:53:31
password:
summary: 树状数组详解
tags:
- Binary Index Tree
- Data Structure
- Algorithm
categories:
- Algorithm
---

一直感觉树状数组用处比较小而且局限、因为最基本的用法就是单点更新和区间求和、但是线段树也能做，只不过代码长一点，但是仔细的去了解了一下树状数组以后发现有很多很赞的地方值得学习。

## 代码模板

### 单点更新、区间求和

```cpp
for(int x = i; x <= n; x += x&-x) c[x] ++; //a[i]单点更新加一
for(int x = r; x ; x -= x&-x) res += c[x]; //求前缀[0,r]
```

### 区间更新、单点查询

+ 对于操作C L R t ：在区间L-R上每个值增加t 

```cpp
for(int x = L; x <= n; x += x&-x) b[x] += t;
for(int x = R+1; x <= n; x += x&-x) b[x] -= t;
```

+ 对于操作Q i   :查询当前a[i]的值

```cpp
int res = a[i];
for(int x = i; x ; x -= x&-x)  res += b[x];
```

### 区间更新、区间查询

+ 对于操作 C L R t    : 对于数组a[n] 区间L-R都增加 t

```cpp
for(int x = L; x <= n; x += x&-x) b[x] += t, c[x] += L*t;
for(int x = R+1; x <= n; x += x&-x) b[x] -= t, c[x] -= (R+1)*t;
```

+ 对于操作 Q L R : 查询数组a[n] 区间L-R的和

```cpp
int res = s[r] - s[l-1];
for(int x = R; x ; x -= x&-x) res += (R+1)*b[x]-c[x];
for(int x = L-1; x ; x -= x&-x) res -= L*b[x] - c[x];
```

## 算法原理

[树状数组基础详解入口](http://www.cnblogs.com/zybgmzl/p/6894426.html?utm_source=itdadao&utm_medium=referral)

下图中:

红色表示树状数组c[i]所覆盖a[n]中的范围 ,其中树状的某一项所覆盖的长度为lowbit(x) = x&-x ,
即 c[x] = a[x-(x&-x)+1]+...+a[x]

蓝色表示当a[i] 需要增加 t 时， 树状数组中哪些项需要做出改变

绿色表示当需要查询某一个区间和时，需要求的两个前缀和及其各自需要通过树状数组的哪些项来进行a[n]的前缀和求和

![树状数组结构图](https://img-blog.csdnimg.cn/20190507222001213.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1Rhd24wMDAw,size_16,color_FFFFFF,t_70)


### 单点修改、区间查询

a[n]: 表示原来的数组，即我们题目中需要修改需要查询的数组 
c[n]: 用来维护a[x]的后缀和(长度为lowbit(x) = x&-x)的树状数组

C i t  --->  a[i] 增加t  --->  在c[n]上操作

for(int x = i; x <= n; x += x&-x) c[x] += t;

Q L R ---> 求a[L] + a[L+1] + ... + a[R] ---> 在c[n]上操作

```cpp
int res = 0;
for(int x = R; x; x -= x&-x) res += c[x];
for(int x = L-1; x; x -= x&-x) res -= c[x];
```

### 区间修改、单点查询

+ a[n]: 原来的数组  设a[0] = 0
+ d[n]: 差分数组  d[i] = a[i]-a[i-1] :   d[1] = a[1]-a[0], d[2] = a[2]-a[1] 

+ b[n]:  用来维护d[x]的后缀和(长度为lowbit(x) = x&-x)的树状数组

```
Sd[n] = d[1] + d[2] + d[3] + ...+ d[n]
        = a[1]-a[0] + a[2]-a[1] + a[3]-a[2]+...+a[n-1]-a[n-2] + a[n]-a[n-1]
        = a[n] - a[0]
        = a[n]
```

即  $ a_{n} = \sum_{i=1}^{n} d_{i} $


+ 区间修改

L R t ---> a[L] - a[R] 每一项都加t  --->   因为 d[i] = a[i] - a[i-1]，所以我们使用d[n]的变化来存储a[n]的变化   
--->  d[R+1] = a[R+1] - a[R] : d[n]数组变化为 ： d[R+1] = d[R+1] - t;
--->  对于 L < k <= R ： d[k] = a[k] - a[k-1]: 因为a[k] 和 a[k-1] 都加了t,所以d[k] 不变
--->  d[L]   = a[L] - a[L-1] : d[n]数组变化为 ：d[L] = d[L] + t

 ---> d[R+1]  减少t ---> 在b[n]上操作
 ---> d[L]       增加t ---> 在b[n]上操作

即在b[n]上进行两个单点更新操作

```cpp
for(int x = R+1; x <= n; x += x&-x)  b[x] -= t;
for(int x = L; x <= n; x += x &-x)   b[x] += t;
```

+ 单点查询

i  ---> 求 a[i] = d[1] + d[2] + ... d[n] ---> 在b[n]上求和操作

```cpp
int res = a[i];
for(int x = i; x ; x -= x & -x) res += b[x];
```

### 区间修改、区间查询

+ a[n]: 原来的数组  设a[0] = 0
+ d[n]: 差分数组  d[i] = a[i]-a[i-1] : d[1] = a[1]-a[0], d[2] = a[2]-a[1] 

+ b[n]:  用来维护d[x]的后缀和(长度为lowbit(x) = x&-x)
+ c[n]:  用来维护e[x] = x*d[x]的后缀和(长度为lowbit(x) = x&-x)

求 a[1] + a[2] + a[3] +...+ a[n] =?

```
原式 = sd[1] + sd[2] + sd[3] + sd[4] + ... + sd[n]  
     = d[1]  + d[1]+d[2] + ... +(d[1] + d[2] + d[3] + ... d[n])  
     = n * d[1] + (n-1) * d[2] +...+ 1 * d[n]  
     = (n+1)(d[1]+d[2]+..+d[n]) - (1*d[1] + 2*d[2] + ... + n*d[n])   
```

即在b[n]上进行一个区间求和操作和在c[n]上进行一个区间求和操作即可

即：
$ \because a[i] = \sum_{j=1}^{i}d[j] $
$ \therefore \sum_{i=1}^{n}a[i] = \sum_{i=1}^{n}\sum_{j=1}^{i}d[j] = \sum_{i=1}^{n}(n-i+1) * d[i] = n * \sum_{i=1}^{n} d[i] - \sum_{i=1}^{n}i*d[i] $


+ 区间修改

C L R t 

```cpp
for(int x = L; x <= n; x += x&-x) b[x] += t, c[x] += L*t;
//为什么这里是L*t ?而不是x*t ? 因为我们是在维护e[L]增加L*t,即T=L*t
for(int x = R+1; x <= n; x += x&-x) b[x] -= t, c[x] -= (R+1)*t;
```

+ 区间查询

Q L R

```cpp
int res = s[r] - s[l-1];
for(int x = R; x ; x -= x&-x) res += (R+1)*b[x]-c[x];
for(int x = L-1; x ; x -= x&-x) res -= L*b[x] - c[x];    
```

## 例题

例题： [POJ 3468 A Simple Problem with Intergers](http://poj.org/problem?id=3468)

```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
using namespace std;
const int maxn = 1e5+100;
const int INF = 0x3f3f3f3f;
typedef long long LL;
typedef pair<int,int> P;

#define PI 3.1415926
#define sc(x)  scanf("%d",&x)
#define pf(x)  printf("%d",x)
#define pfn(x) printf("%d\n",x)
#define pfs(x) printf("%d ",x)
#define rep(n) for(int i = 0; i < n; i++)
#define per(n) for(int i = n-1; i >= 0; i--)
#define mem(a,x) memset(a,x,sizeof(a))

int n,q;
LL b[maxn],c[maxn],s[maxn];
int a[maxn];

int main()
{
  sc(n),sc(q);
  mem(s,0LL);
  rep(n) {sc(a[i+1]);s[i+1] = s[i] + a[i+1];}
  mem(b,0LL);
  mem(c,0LL);
  rep(q)
  {
    char key[2];
    int l,r,d;
    scanf("%s%d%d",key,&l,&r);
    if(key[0] == 'C')
    {
      sc(d);
      for(int x = l;x <= n; x += x&-x) {b[x] += d; c[x] += l * d;}
      for(int x = r+1;x <= n; x += x&-x) {b[x] -= d; c[x] -= (r+1) * d;}
    }
    else
    {
      LL res = s[r]-s[l-1];
      //res = sum(r) - sum(l-1)   
      //s[x] = ssb[i] = (x+1)sb[i] - isb[i] = (x+1)sb[i] - sc[i];
      for(int x = r; x ; x -= x&-x)  res += ((r+1) * b[x] - c[x]);
      for(int x = l-1; x ; x -= x&-x) res -= (l * b[x] - c[x]);
      printf("%lld\n",res);
    }
  }
    return 0;
}
```
