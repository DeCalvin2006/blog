---
title: "洛谷 P8618\_[蓝桥杯 2014 国 B] Log 大侠 题解"
tags:
  - 线段树
  - 技巧
date: 2022-10-31 15:01:40
---

## 题目大意

给出一个长度为 $n$ 的数列，有 $m$ 次操作，每次给出一个区间 $[l,r]$，把这个区间内的数字 $a_i$ 变成 $\lfloor \log_2a_i+1\rfloor$。每次操作后输出数列的和。

## 思路

容易发现

$$\log_2 2 + 1 = 2$$

和

$$\log_2 1 + 1 = 1$$

所以，对一个只有 $1$ 和 $2$ 的区间进行操作之后没有变化。

考虑如何优化掉这类操作。

使用线段树进行维护，除了维护区间和以外，还要维护一个区间最大值，当这个最大值小于等于 $2$ 时就说明这个区间只有小于等于 $2$ 的数，此时就可以停止操作了。

如果不是的话，我们可以直接暴力处理。对于一个小于等于 $10^9$ 的数，对他取 $\log_2$ 进行 $30$ 次必定会变成 $2$。这样可以保证时间复杂度的正确性。

每次操作后输出 `t[1]` （也就是线段树的根，代表区间 $[1,n]$ 的和）即可。

## Code

```C++
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
ll read() {
  ll x = 0, f = 1;
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
int n, m;
ll a[N];
const int T = N << 2;
ll sumt[T], maxt[T];
inline int lc(int x) { return x << 1; }
inline int rc(int x) { return x << 1 | 1; }
inline int mp(int l, int r) { return (l + r) >> 1; }
inline void pushUp(int x) {
  sumt[x] = sumt[lc(x)] + sumt[rc(x)];
  maxt[x] = max(maxt[lc(x)], maxt[rc(x)]);
}
void build(int l, int r, int x) {
  if (l == r)
    sumt[x] = maxt[x] = a[l];
  else {
    int mid = mp(l, r);
    build(l, mid, lc(x));
    build(mid + 1, r, rc(x));
    pushUp(x);
  }
}
void update(int L, int R, int l, int r, int x) {
  if (maxt[x] <= 2)
    return;
  if (l == r)
    sumt[x] = floor(log2(sumt[x]) + 1), maxt[x] = sumt[x];
  else {
    int mid = mp(l, r);
    if (L <= mid)
      update(L, R, l, mid, lc(x));
    if (R > mid)
      update(L, R, mid + 1, r, rc(x));
    pushUp(x);
  }
}
int main() {
  n = read(), m = read();
  for (int i = 1; i <= n; i++)
    a[i] = read();
  build(1, n, 1);
  for (int i = 1; i <= m; i++) {
    int l = read(), r = read();
    update(l, r, 1, n, 1);
    cout << sumt[1] << endl;
  }
  return 0;
}
```
