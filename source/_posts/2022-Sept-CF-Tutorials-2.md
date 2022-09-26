---
title: 最近CF做题的一些题解②:698A,1646C,665C
tags:
  - Codeforces
  - 题解
  - DP
categories:
  - CF题解合集
date: 2022-09-26 09:28:33
---

这是 Codeforces 题解系列（因为实在可能太多了）的第 ② 篇。

<!-- more -->

# 698 A. Vacations

[题面链接](https://codeforces.com/contest/698/problem/A)(Rating:1400)

> 状态设计的注意事项。

## Problem

每天可以有三种选择：

- 如果健身房开，则可以选择健身。
- 如果有比赛，则可以选择打比赛。
- 可以休息。

给出每天健身房和比赛的状态，在不连续两天健身或者打比赛的前提下，求最少休息几天。

## Solution

考虑将上一天的活动存在状态中。
$f_{i,j,k}\ (j,k\in\{0,1\})$ 为前 i 天，连续运动 j 天，打比赛 k 天的最小休息天数。
dfs 转移即可。

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
int n;
int state[100 + 10];
int dp[100 + 10][3][3];
int dfs(int x, int sports, int contest) {
  if (x == 0)
    return 0;
  if (dp[x][sports][contest] != -1) {
    return dp[x][sports][contest];
  } else {
    int ans = 0x3f3f3f3f;
    switch (state[x]) {
    case 0: {
      ans = dfs(x - 1, 0, 0) + 1;
      break;
    }
    case 1: {
      if (contest < 1) {
        ans = min(dfs(x - 1, 0, 0) + 1, dfs(x - 1, 0, 1));
      } else {
        ans = dfs(x - 1, 0, 0) + 1;
      }
      break;
    }
    case 2: {
      if (sports < 1)
        ans = min(dfs(x - 1, 0, 0) + 1, dfs(x - 1, 1, 0));
      else {
        ans = dfs(x - 1, 0, 0) + 1;
      }
      break;
    }
    case 3: {
      if (sports < 1)
        ans = min(ans, dfs(x - 1, 1, 0));
      if (contest < 1) {
        ans = min(ans, dfs(x - 1, 0, 1));
      }
      ans = min(ans, dfs(x - 1, 0, 0) + 1);
      break;
    }
    }
    return dp[x][sports][contest] = ans;
  }
}
int main() {
  // TODO: code here
  n = read();
  for (int i = 1; i <= n; i++) {
    state[i] = read();
  }
  for (int i = 1; i <= n; i++) {
    for (int j = 0; j < 3; j++) {
      for (int k = 0; k < 3; k++) {
        dp[i][j][k] = -1;
      }
    }
  }
  cout << dfs(n, 0, 0) << endl;
  return 0;
}
```

# 1646 C. Factorials and Powers of Two

[题面链接](https://codeforces.com/contest/1646/problem/C)(Rating:1500)

> 其实这不是个 dp 题。

## Problem

定义$\text{Powerful Number}$为阶乘或者 2 的幂。
给出一个数$n$，求最少的$\text{Powerful Number}$拆分，或者无解输出$-1$.

## Solution

首先由于二进制拆分，可以得到一定有一个解存在。
$n\le 10^{12}$,那么这其中的阶乘数只有 15 个。去掉 1 和 2 以后只剩 13 个。
每次枚举减掉一个最大的阶乘数，然后剩下的进行二进制拆分。
复杂度接近$O(\log_2n)$。

```c++
#include <bits/stdc++.h>
#define int unsigned long long
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
const int N = 15;
int fact[N];
int n;
int popcount(int x) {
  int cnt = 0;
  while (x) {
    x -= (x & (-x));
    cnt++;
  }
  return cnt;
}

signed main() {
  n = read();
  fact[0] = 1;
  for (int i = 1; i < N; i++) {
    fact[i] = fact[i - 1] * i;
  }
  while (n--) {
    int k = read();
    int ans = __builtin_popcountll(k);
    // clog << "Bitwise: " << ans << endl;
    for (int i = 0; i < (1 << N); i++) {
      int sum = 0, cnt = 1;
      int tmpi = i;
      while (tmpi) {
        if (tmpi & 1) {
          sum += fact[cnt];
        }
        tmpi >>= 1;
        cnt++;
      }
      if (k >= sum) {
        ans = min(ans, (unsigned long long)__builtin_popcountll(i) +
                           __builtin_popcountll(k - sum));
      }
    }
    cout << ans << endl;
  }
  // TODO: code here
  return 0;
}
```

# 665 C. Simple Strings

[题面链接](https://codeforces.com/contest/665/problem/C)(Rating:1300)

> 这也 1300 就离谱。

## Problem

给出一个字符串，每次可以把一个字符替换为另一个字符。
求使得每对相邻字符都不同的最小操作次数。输出操作后的字符串。

## Solution

我觉得大概不需要题解了吧。

```C++
#include <bits/stdc++.h>
#include <random>
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
int n;
char buf[2000000 + 10];
char otherchar(int x) {
  mt19937 lmt((random_device{})()); // LMT AK CSP/NOIP/SDOI/NOI/CTSC/IOI!
  uniform_int_distribution<char> u('a', 'z');
  char c = u(lmt);
  while (c == buf[x - 1] || c == buf[x + 1]) {
    c = u(lmt);
  }
  return c;
}
int main() {
  cin >> (buf + 1);
  n = strlen(buf + 1);
  for (int i = 1; i <= n; i++) {
    if (buf[i] == buf[i - 1]) {
      buf[i] = otherchar(i);
    }
  }
  cout << buf + 1 << endl;
  return 0;
}
```
