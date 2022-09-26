---
title: 最近CF做题的一些题解①:1706B,545C
tags:
  - Codeforces
  - 题解
  - DP
categories:
  - CF题解合集
date: 2022-09-25 21:28:16
---

最近做了不少 CF 上的 DP 题（PS：标签事 dp，但是实际上却是各种各样的东西）。
可能要写很多篇分着写 QwQ，争取在 9 月结束之前写完吧。

<!-- more -->

# 1706 B. Making Towers

[题面链接](https://codeforces.com/contest/1706/problem/b) (Rating:1100)

> 比较有技术含量的一道题。

## Problem

给定 n 个方块，最多有$n$种不同的颜色。每次可以选一个方块将其放在上、左、右三个方向。定义塔为$y$轴方向上连续的一段同色小方块。对于$1 \to n$中的每一种颜色，输出该颜色最高的塔高度。

## Solution

思路其实很简单。对于颜色不同的方块，我们可以用如下图的策略把他放在侧面，这样可以略过中间的偶数个方块。

```plaintext
Blocks: 1 1 2 3 1
 +-+-+
 |3|1|
 +-+_+
 |2|1|
 +-+-+
   |1|
   +-+
```

经过找规律我们发现，这个规律可以整合为：

> 奇偶性相同的方块，必定能通过某些方式被放到同一列上。

记$f_{p,j}$为奇偶性为$p$的位置上，颜色为 i 的塔的最大高度。
可得转移方程为
$$\text{For }i\text{ in }1\to n,\ f_{i\bmod 2,color_i}=max(f_{i\bmod2,color_i}+1,f_{i+1\bmod2,color_i})$$

也就是说，每一个位置的最大值有两种情况：

- 前面有奇偶性相同且颜色相同的位置。此时可以把 i 接上去，高度加一。
- 前面的不同奇偶性的塔高度更高。此时放弃这座塔，用不同奇偶性的塔去更新。
  最后答案就是$max(f_{0,1},f_{1,1}),max(f_{0,2},f_{1,2}),max(f_{0,3},f_{1,3}) \cdots$

## Code

```c++
#include <bits/stdc++.h>
using namespace std;
int read() {
  int x = 0, f = 1;
  char c = getchar();
  while (c > '9' || c < '0') {
    if (c == '-')
      f = -1;
    c = getchar();
  }
  while (c >= '0' && c <= '9') {
    x = (x << 1) + (x << 3) + c - '0';
    c = getchar();
  }
  return x * f;
}
const int N = 1E5 + 10;
int t = read();
int color[N];
int dp[2][N];
int main() {
  while (t--) {
    int n = read();
    for (int i = 1; i <= n; i++) {
      color[i] = read();
      dp[1][i] = dp[0][i] = 0;
    }
    for (int i = 1; i <= n; i++) {
      dp[i % 2][color[i]] =
          max(dp[i % 2][color[i]], dp[(i % 2) ^ 1][color[i]] + 1);
    }
    for (int i = 1; i <= n; i++) {
      cout << max(dp[1][i], dp[0][i]) << " \n"[i == n];
    }
  }
  return 0;
}
```

# 545 C. Woodcutters

[题面链接](https://codeforces.com/contest/545/problem/c) (Rating:1500) _(某同学:这不是 2000?)_

> 状态转移的艺术。

## Problem

给出 n 棵树的横坐标$x_i$和高度$h_i$。对每棵树，可以有三种选择：

- 砍掉他，向左倒，形成一个区间$[x_i-h_i,x_i]$。
- 砍掉他，向右倒，形成一个区间$[x_i,x_i+h_i]$。
- 不砍他，形成区间（此时退化为一个点）$[x_i,x_i]$。
  所有以上的操作形成的区间不能重叠。求最多砍倒多少树。

## Solution

设$f_{i,type}$为前$i$棵树，第$i$棵树进行操作$type$的最大值，其中$type$为$\text{LEFT,MIDDLE,RIGHT}$中的一个。
那么状态转移方程可以写作三部分：

- $(i,\text{MIDDLE})$:$\text{Simplest}$。
  - 首先一定可从$(i-1,\text{LEFT or MIDDLE})$转移过来。
  - 如果$i-1$可以安全的向右倒，不会碰到$i$这棵树，那就可以从$(i-1,\text{RIGHT})$过来。
- $(i,\text{LEFT})$:$\text{Harder}$.
  - 可以从左边安全转移。也就是$i-1$向右不会和$i$重合。
    - 从$(i-1,\text{LEFT})+1$和从$(i-1,\text{MIDDLE})+1$转移。
    - 同时，如果$i-1$可以向右倒，并且$i$可以向左倒，那么可以从$(i-1,\text{RIGHT})+1$过来。
  - 否则为$0$。
- $(i,\text{RIGHT})$:$\text{Same as above, but reverse.}$
  答案为$max{(i,\text{LEFT}),(i,\text{MIDDLE}),(i,\text{RIGHT})}$。

## Code

```C++
#include <bits/stdc++.h>
using namespace std;
int read() {
  int x = 0, f = 1;
  char c = getchar();
  while (c > '9' || c < '0') {
    if (c == '-')
      f = -1;
    c = getchar();
  }
  while (c >= '0' && c <= '9') {
    x = (x << 1) + (x << 3) + c - '0';
    c = getchar();
  }
  return x * f;
}
const int N = 1E5 + 10;
int n;
int x[N], h[N];
int dp[N][3];
enum { LEFT = 0, MID = 1, RIGHT = 2 };
int main() {
  n = read();
  for (int i = 1; i <= n; i++) {
    x[i] = read(), h[i] = read();
  }
  dp[1][MID] = 0;
  dp[1][LEFT] = 1;
  if (n > 1 && x[1] + h[1] >= x[2]) {
    dp[1][RIGHT] = 0;
  } else {
    dp[1][RIGHT] = 1;
  }
  for (int i = 2; i <= n; i++) {
    // MID
    dp[i][MID] = max(dp[i - 1][MID], dp[i - 1][LEFT]);
    if (x[i - 1] + h[i - 1] < x[i]) {
      dp[i][MID] = max(dp[i][MID], dp[i - 1][RIGHT]);
    }
    // LEFT
    // Case 1: x[i]-h[i] <= x[i-1]: Ileggal
    if (x[i] - h[i] <= x[i - 1]) {
      dp[i][LEFT] = 0;
    } else {
      // Case 2: x[i]-h[i] > x[i-1];
      dp[i][LEFT] = max(dp[i - 1][LEFT], dp[i - 1][MID]) + 1;
      if (x[i - 1] + h[i - 1] < x[i] - h[i]) {
        // Case 3: x[i]-h[i] > x[i-1]+h[i-1]
        dp[i][LEFT] = max(dp[i][LEFT], dp[i - 1][RIGHT] + 1);
      }
    }
    // RIGHT
    // Case 1: x[i]+h[i]>=x[i+1]: Illegal
    if (i != n && x[i] + h[i] >= x[i + 1]) {
      dp[i][RIGHT] = 0;
    } else {
      // Case 2: x[i]+h[i]<x[i+1]
      dp[i][RIGHT] = max(dp[i - 1][LEFT], dp[i - 1][MID]) + 1;
      if (x[i - 1] + h[i - 1] < x[i]) {
        // Case 3: x[i]-h[i] > x[i-1]+h[i-1]
        dp[i][RIGHT] = max(dp[i][RIGHT], dp[i - 1][RIGHT] + 1);
      }
    }
  }
  cout << max(dp[n][LEFT], max(dp[n][MID], dp[n][RIGHT])) << endl;
  return 0;
}
```
