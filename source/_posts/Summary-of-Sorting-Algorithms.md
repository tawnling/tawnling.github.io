---
title: Summary of Sorting Algorithms
top: true
cover: false
toc: true
mathjax: false
img: https://i.loli.net/2019/12/27/q132ijNetGJZ9dh.jpg
date: 2019-09-27 01:01:16
password:
summary: 内部排序算法总结
tags:
- template
- Sorting
- Algorithm
- Summary
categories:
- Algorithm
---

# <center>内部排序算法总结

## 介绍
排序算法主要可以分为内部排序和外部排序，其中内部排序由可以分为非线性时间比较排序和线性时间非比较排序，前者主要通过比较来决定元素之间的相对次序，都有关键字的比较和记录的移动，其时间复杂度不能突破O(nlogn),后者不需要通过比较来进行,可以以线性时间运行。

## 分类
非线性时间比较排序：  
      &emsp;&emsp;&emsp;&emsp;插入排序：简单插入排序、折半插入排序、希尔排序  
      &emsp;&emsp;&emsp;&emsp;交换排序：冒泡排序、快速排序  
      &emsp;&emsp;&emsp;&emsp;选择排序：直接选择排序、堆排序  
      &emsp;&emsp;&emsp;&emsp;归并排序：二路归并排序、多路归并排序(下文未介绍)  
线性时间非比较排序：
      基数排序、计数排序、桶排序

不稳定排序：希尔排序、快速排序、直接选择排序、堆排序
其余为稳定排序

## 复杂度&&稳定性


|排序算法|平均时间复杂度|最好时间复杂度|最坏时间复杂度|空间复杂度|稳定性|
|-|-|-|-|-|-|
|简单插入排序|O(n^2)|O(n)|O(n^2)|O(1)|稳定|
|折半插入排序|O(n^2)|O(n)|O(n^2)|O(1)|稳定|
|希尔排序|O(n^(1.3))|O(n)|O(n^2)|O(1)|不稳定|
|冒泡排序|O(n^2)|O(n)|O(n^2)|O(1)|稳定|
|快速排序|O(nlogn)|O(nlogn)|O(n^2)|O(logn)|不稳定|
|直接选择排序|O(n^2)|O(n^2)|O(n^2)|O(n^2)|不稳定|
|堆排序|O(nlogn)|O(nlogn)|O(nlogn)|O(1)|不稳定|
|二路归并排序|O(nlogn)|O(nlogn)|O(nlogn)|O(n)|稳定|
|基数排序|O(d(n+r))|O(d(n+r))|O(d(n+r))|O(rd)|稳定|
|计数排序|O(n+k)|O(n+k)|O(n+k)|O(n+k)|稳定|
|桶排序|O(n+k)|O(n+k)|O(n^2)|O(k)|稳定|


## 讲解&代码

### Start

1. 数组中的元素默认放在从a[1]开始
2. 以下排序都是基于顺序存储实现，非链式，有些算法可以修改成链式
3. 总共有以下几类排序
  1. 直接插入排序
  2. 折半插入排序
  3. 希尔排序
   **1-3属于插入排序**
  4. 冒泡排序
  5. 快速排序
   **4-5属于交换排序**
  6. 直接选择排序
  7. 堆排序
   **6-7属于选择排序**
  8. 二路归并排序
   **8属于归并排序**
   **1-8属于非线性时间比较排序**
  9. 基数排序
  10. 计数排序
  11. 桶排序
  **9-11属于线性时间非比较排序**

4. 代码默认环境

```cpp
#include <bits/stdc++.h>

using namespace std;
const int maxn = 1e5 + 10;

```

### 直接插入排序

+ 直接插入排序
+ 平均时间复杂度：O(n^2)
+ 最好时间复杂度：O(n)   初始序列为正序
+ 最坏时间复杂度：O(n^2) 初始序列为逆序
+ 空间复杂度：O(1)
+ 稳定性：稳定

```cpp
void Straight_Insert_Sort(int* a, int n)
{
  for(int i = 2; i <= n; i++)
    {
      if(a[i] < a[i-1])
      {
        int j;
        a[0] = a[i]; //设置哨兵
        for(j = i-1; a[j] > a[0]; j--)
           a[j+1] = a[j];
        a[j+1] = a[0];
      }
    }
}
```

### 折半插入排序

+ 平均时间复杂度：O(n^2)
+ 最好时间复杂度：O(n)   初始序列为正序
+ 最坏时间复杂度：O(n^2)
+ 空间复杂度：O(1)
+ 稳定性：稳定
注：折半插入排序是插入排序的一个优化算法，主要是减少了寻找插入位置时的比较次数，但是元素的移动次数仍然不变，所以总的时间复杂度仍然是O(n^2)


```cpp
void Binary_Insert_Sort(int *a, int n)
{
  for(int i = 2; i <= n; i++)
   {
     if(a[i] < a[i-1])
     {
       a[0] = a[i];
       int l = 1, r = i-1;
       while(l <= r)
       {
         int m = l + (r-l)/2;
         if(a[m] <= a[0]) l = m + 1;
         else r = m - 1;
       }
       int j;
       for(j = i-1; j >= l; j--)
           a[j+1] = a[j];
       a[j+1] = a[0];
     }
   }
}
```

### 希尔排序

+ 平均时间复杂度：O(n^(1.3—2))
+ 最好时间复杂度：O(n)   初始序列为正序
+ 最坏时间复杂度：O(n^2)
+ 空间复杂度：O(1)
+ 稳定性：不稳定
注：
1. 希尔排序也属于插入排序的一种，又称“缩小增量排序”(Deminishing Increment Sort)
基本思想是将数列等步长分成若干组，组内进行插入排序，步长不断减小，直至1，这样就可以减少参与直接插入排序的数据量，有利于减小比较和移动的次数，从而缩减时间复杂度。
其时间复杂度和选取得增量序列有关
希尔增量: ht = N / 2, h[k] = h[k+1] / 2，即{N/2, (N / 2)/2, ..., 1}
Hibbard：{1, 3, ..., 2^k-1};
Sedgewick：{1, 5, 19, 41, 109...}该序列中的项或者是9*4^i - 9*2^i + 1或者是4^i - 3*2^i + 1。
2. 以下代码非使用监视哨算法，使用监视哨算法可以缩减一定的测试查找循环时间，解决边界问题。但是估计会需要使用O(inc)的代价。

```cpp
void Shell_Sort(int *a,int n)
{
 int inc = n;
 do {
   inc = inc/3 + 1;
   for(int i = 1+inc; i <= n; i++)
   {
     if(a[i] < a[i-inc])
    {
      int j;
      a[0] = a[i];
      for(j = i-inc; j > 0 && a[j] > a[0]; j -= inc)
            a[j+inc] = a[j];
      a[j+inc] = a[0];
    }
  }
  } while(inc > 1);
}
```

### 冒泡排序

+ 平均时间复杂度：O(n^2)
+ 最好时间复杂度：O(n)   初始序列为正序
+ 最坏时间复杂度：O(n^2)
+ 空间复杂度：O(1)
+ 稳定性：稳定

```cpp
// 最简单的交换排序，严格上不算冒泡排序，不满足“两两比较相邻记录”
void Bubble_Sort_A(int *a, int n)
{
  for(int i = 1; i < n; i++)
    for(int j = i+1; j <= n; j++)
       if(a[i] > a[j]) swap(a[i],a[j]);
}

//冒泡排序
void Bubble_Sort_B(int *a, int n)
{
  for(int i = 1; i < n; i++)
    for(int j = n-1; j >= i; j--)
      if(a[j] > a[j+1])
         swap(a[j],a[j+1]);
}

//优化后的冒泡排序
void Bubble_Sort_C(int *a, int n)
{
  bool flag = true;
  for(int i = 1; i < n && flag; i++)
    {
    flag = false;
    for(int j = n-1; j >= i; j--)
     if(a[j] > a[j+1])
       {
         swap(a[j],a[j+1]);
         flag = true;
       }
    }
}
```


### 快速排序

+ 平均时间复杂度：O(nlogn)
+ 最好时间复杂度：O(nlogn)
+ 最坏时间复杂度：O(n^2) 初始序列为正序
+ 空间复杂度：O(logn)
+ 稳定性：稳定
注：
1. 快速排序是通过一趟排序将序列分为两部分，一部分均比关键字小，一部分均比关键字大，
然后再对这两部分分别进行快速排序。

2. 时间复杂度分析
我们来利用Master主定理分析
Master 主定理:  对于T[n] = aT[n/b] + f(n) 并且f(n) 是一个渐进正函数，则有一下三种情况：
  1. 如果f(n) = O(n^(logb(a-c))) 对于某个常量c>0成立,那么T[n] = O(n^(logb(a)))
  2. 如果f(n) = O(n^(logb(a))), 那么T[n] = O(n^(logb(a)) * logn)
  3. 如果f(n) = O(n^(logb(a+c))) 对于某个常量c>0成立,并且af(n/b) <= cf(n) 对于某个常量c < 1 (n足够大) 成立， 那么T[n] = O(f(n))

  对于快速排序，T[n] = 2T[n/2] + f(n)
  a = 2, b = 2 , logb(a) = 1; 所以 f(n) =  O(n^(logb(a))) = n; 所以T(n) = n*logn

  最坏情况时， 在原序列有序时，T[n] = T[n-1] + T[1] + O(n)
  对于这种情况，主观分析：因为每次只排出一个，相当于没做，所以复杂度为O(n^2))

3. 快速排序的空间复杂度O(logn)主要是因为递归调用需要保持一些数据
4. 优化：
  1. 枢轴的选取方式的优化：枢轴的选取方式有：(1) 固定位置选取；(2) 随机位置选取； (3) 三值取中法 等
  2. 优化小数组时的排序方案：当局部排序数组长度较小时，采用插入排序，而非快速排序，因为长度分割到够小后，继续分割的效率要低于直接插入排序。
  3. 略去不必要的交换：略去不必要的交换，将交换操作改为替换操作。因为交换操作需要进行3次赋值操作，而替换操作只需要进行1次赋值操作。
```cpp
// 0,l,r 相互配合组成哨兵
void Quick_Sort(int *a, int L, int R)
{
  if(L >= R) return ;
  a[0] = a[L];
  int l = L, r = R;
  while(l < r)
  {
    while(l < r && a[r] >= a[0]) r--;
    a[l] = a[r];
    while(l < r && a[l] <= a[0]) l++;
    a[r] = a[l];
  }
  a[l] = a[0];
  Quick_Sort(a, L, l-1);
  Quick_Sort(a, l+1, R);
}
```


### 直接选择排序

+ 平均时间复杂度：O(n^2)
+ 最好时间复杂度：O(n^2)
+ 最坏时间复杂度：O(n^2)
+ 空间复杂度：O(1)
+ 稳定性：不稳定

```cpp
void Selec_Sort(int *a, int n)
{
  for(int i = 1; i < n; i++)
    {
      int tag = i;
      for(int j = i+1; j <= n; j++)
        if(a[j] < a[tag]) tag = j;
      swap(a[i],a[tag]);
    }
}
```


### 堆排序

+ 平均时间复杂度：O(nlogn)
+ 最好时间复杂度：O(nlogn)
+ 最坏时间复杂度：O(nlogn)
+ 空间复杂度：O(1)
+ 稳定性：不稳定

```cpp
//s 是子堆的起点，m 是堆的数据量
void Heap_Adjust(int *a, int s, int m)
{
  a[0] = a[s];
  for(int i = 2*s; i <= m; i <<= 2)
  {
    //比较左右节点的大小
    if(i < m && a[i+1] > a[i])
      i++;
    if(a[i] <= a[s]) break;
    a[s] = a[i];
    s = i;
  }
  a[s] = a[0];
}

void Heap_Sort(int *a, int n)
{
  for(int i = n/2; i > 0; i--)
    Heap_Adjust(a,i,n);
  for(int i = n; i > 1; i--)
  {
    swap(a[1],a[i]);
    Heap_Adjust(a,1,i-1);
  }
}
```


### 归并排序(二路)

+ 平均时间复杂度：O(nlogn)
+ 最好时间复杂度：O(nlogn)
+ 最坏时间复杂度：O(nlogn)
+ 空间复杂度：O(n)
+ 稳定性：稳定

```cpp
void Merge_Sort(int *a, int L, int R)
{
  if(L >= R) return ;
  int m = L + (R-L)/2;
  int n1 = m-L+1, n2 = R-m;

  Merge_Sort(a,L,m);
  Merge_Sort(a,m+1,R);

  int *l = (int*)malloc(sizeof(int)*(n1));
  int *r = (int*)malloc(sizeof(int)*(n2));

  for(int i = 0; i < n1; i++)
    l[i] = a[L+i];
  for(int i = 0; i < n2; i++)
    r[i] = a[m+i+1];

  int i = 0,j = 0,k = L;
  while(i < n1 && j < n2)
      a[k++] = l[i] <= r[j] ? l[i++] : r[j++];
  while(i < n1)
      a[k++] = l[i++];
  while(j < n2)
      a[k++] = r[j++];
  free(l);
  free(r);
}
```

### 基数排序

+ 平均时间复杂度：O(d(n+r))
+ 最好时间复杂度：O(d(n+r))
+ 最坏时间复杂度：O(d(n+r))
+ 空间复杂度：O(rd)
+ 稳定性：稳定
注：不能对负数进行处理，如果要处理负数可以统一先加一个大整数，最后再统一减去。
d个关键码，关键码的范围是0-r

```cpp
void Radix_Sort(int *a, int n)
{
  int maxa = a[1],d = 0,exp = 1;
  int *count = new int[n+1];
  for(int i = 2; i <= n; i++)
     maxa = max(maxa,a[i]);
  while(maxa) maxa /= 10,d++;
  for(int i = 1; i <= d; i++, exp *= 10)
    {
      int range[10] = {0};
      for(int i = 1; i <= n; i++)
        range[a[i]/exp%10]++;
      for(int i = 1; i <= 9; i++)
        range[i] += range[i-1];
      for(int i = n; i > 0; i--)
        count[range[a[i]/exp%10]--] = a[i];
      for(int i = 1; i <= n; i++)
        a[i] = count[i];
    }
  delete[] count;
}
```


### 计数排序

+ 平均时间复杂度：O(n+k)
+ 最好时间复杂度：O(n+k)
+ 最坏时间复杂度：O(n+k)
+ 空间复杂度：O(n+k)
+ 稳定性：稳定
注：计数排序是一个非基于比较的算法，前面的算法中，除了基数排序，其他排序都是基于比较的算法，都有关键字的比较和记录的移动,基于比较的的排序算法的时间复杂度是O(nlogn),如堆排序和归并排序。计数排序的时间复杂度是O(n+k)，k是数据的范围，其快于任何一种基于比较的算法，其是牺牲空间换取时间的做法，当O(k) > O(nlogn)时反而慢于基于比较的排序算法。
前面的基数排序也是基于基数排序实现的，只不过其k <= 9。
计数排序的稳定性：range[i] 表示小于等于i的元素个数，所以倒着遍历保证稳定性。

```cpp
void Count_Sort(int *a, int n)
{
  int k = a[1];
  for(int i = 2; i <= n; i++)
     k = max(k,a[i]);
  int *range = new int[k+1]();
  int *count = new int[n+1];
  for(int i = 1; i <= n; i++)
    range[a[i]] ++;
  for(int i = 1; i <= k; i++)
    range[i] += range[i-1];
  for(int i = n; i >= 1; i--)
    count[range[a[i]]--] = a[i];
  for(int i = 1; i <= n; i++)
    a[i] = count[i];
  delete[] range;
  delete[] count;
}
```

### 桶排序

+ 平均时间复杂度：O(n+k)
+ 最好时间复杂度：O(n+k)
+ 最坏时间复杂度：O(n^2)
+ 空间复杂度：O(k)
+ 稳定性：稳定

```cpp
void Bucket_Sort(int *a, int n)
{
  int k = a[1];
  for(int i = 2; i <= n; i++)
    k = max(k,a[i]);
  int *range = new int[k+1]();
  for(int i = 1; i <= n; i++)
    range[a[i]]++;
  int idx = 1;
  for(int i = 0; i <= k; i++)
    while(range[i]--)
      a[idx++] = i;
  delete[] range;
}
```

### End

```cpp
int a[maxn],n;
int main()
{
  cin >> n;
  for(int i = 1; i <= n; i++)
     cin >> a[i];
  //插入排序
  //Straight_Insert_Sort(a,n);
  //Binary_Insert_Sort(a,n);
  //Shell_Sort(a,n);
  //交换排序
  //Bubble_Sort_A(a,n);
  //Bubble_Sort_B(a,n);
  //Bubble_Sort_C(a,n);
  //Quick_Sort(a,1,n);
  //选择排序
  //Selec_Sort(a,n);
  //Heap_Sort(a,n);
  //归并排序
  //Merge_Sort(a,1,n);
  //基数排序
  //Radix_Sort(a,n);
  //计数排序
  //Count_Sort(a,n);
  //桶排序
  //Bucket_Sort(a,n);
  for(int i = 1; i <= n; i++)
     cout << a[i] << " ";
  cout << endl;
  return 0;
}

```
