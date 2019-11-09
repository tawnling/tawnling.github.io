---
title: Leetcode Concurrency Problems
top: false
cover: false
toc: true
mathjax: false
date: 2019-11-07 10:59:06
img:
password:
summary: LeetCode 并发编程题目题解，持续更新
tags:
- Leetcode
- Concurrency
categories:
- Others
---
## 1114.按序打印

[题目链接](https://leetcode-cn.com/problems/print-in-order/)


```python
class Foo:
    def __init__(self):
        pass
        self.t = 0

    def first(self, printFirst: 'Callable[[], None]') -> None:
        # printFirst() outputs "first".
        # Do not change or remove this line.
        printFirst()
        self.t = 1

    def second(self, printSecond: 'Callable[[], None]') -> None:
        while self.t != 1:
            pass
        # printSecond() outputs "second".
        # Do not change or remove this line.
        printSecond()
        self.t = 2

    def third(self, printThird: 'Callable[[], None]') -> None:
        while self.t != 2:
            pass
        # printThird() outputs "third".
        # Do not change or remove this line.
        printThird()
```
