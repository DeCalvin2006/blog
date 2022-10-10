---
title: 最近CF做题的一些题解④:1324D,1675F,1282B2,1681D
tags:
  - Codeforces
  - 题解
  - DP
categories:
  - CF题解合集
date: 2022-09-26 16:28:37
---

这是 Codeforces 题解系列的第 ④ 篇。

<!-- more -->

# 1324 D. Pair of Topics

[题面链接](https://codeforces.com/contest/1324/problem/D) (Rating:1400)

> 构造二分。

## Problem

给出两个数列$a,b$。
定义$\text{GoodPair}$为数对$(i,j)$，其中$i,j$满足$a_i+a_j>b_i+b_j$。
统计$\text{GoodPair}$的个数。

## Solution

把式子移项可得$a_i-b_i>b_j-a_j$。
这时把$a-b$排个序,二分$b_i-a_i+1$即可。

## Code

```C++
#include <bits/stdc++.h>
using namespace std;
#define int long long
const int MAXN = 2E5 + 10;
int a[MAXN];
int b[MAXN];
int ab[MAXN];
int n;
signed main() {
  cin >> n;
  for (int i = 1; i <= n; i++) {
    cin >> a[i];
  }
  for (int i = 1; i <= n; i++) {
    cin >> b[i];
  }
  for (int i = 1; i <= n; i++) {
    ab[i] = a[i] - b[i];
  }
  sort(ab + 1, ab + 1 + n);
  int ans = 0;
  for (int i = 1; i <= n; i++) {
    if (ab[i] <= 0)
      continue;
    // cout << b[i] - a[i] << endl;
    int t = lower_bound(ab + 1, ab + 1 + i, -ab[i] + 1) - (ab);
    // for (int i = 1; i <= t; i++) {
    // cout << ab[t] << ' ';
    //}
    // cout << endl;
    ans += i - t;
  }
  cout << ans << endl;
  return 0;
}
```

# 1675 F. Vlad and Unfinished Business

[题面链接](https://codeforces.com/contest/1675/problem/F) (Rating:1800)

> 一点点图论知识。

## Problem

给定一棵树（边权为$1$）、两个点$x,y$和几个点$a_1,a_2,\dots a_k$，求从$x$出发，经过每个$a_i$，最后在$y$结束的最小路径长度。

## Solution

我们抽象一下这棵树。
![来自样例3](1675F-1.png)
图中红色部分是路径$x\to y$，蓝色方框中是其他节点，青色方框中是目标节点，绿色箭头是最佳路径。
可以看出，最优路径有以下性质：

- 必定包含$x\to y$（以下称为树干）一次且仅一次。
- 从每个目标顶点到树干的路径上必定被包含两次（不重复计算）。

大力$\tt{dfs}$即可。

## Code

```C++
#include <bits/stdc++.h>
#include <cstring>
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
const int N = 2E5 + 10;
int head[N], to[N << 1], nxt[N << 1], w[N << 1], ecnt = 0;
void adde_(int a, int b) {
  ecnt++;
  nxt[ecnt] = head[a];
  head[a] = ecnt;
  to[ecnt] = b;
  w[ecnt] = 1;
}
void adde(int a, int b) { adde_(a, b), adde_(b, a); }
int t;
int n, k, x, y;
int a[N];
int dis[N];
bool dfs1(int x, int fa) {
  bool c = x == y;
  for (int i = head[x]; i; i = nxt[i]) {
    int v = to[i];
    if (v != fa) {
      dis[v] = dis[x] + w[i];
      bool tmp = dfs1(v, x);
      if (tmp) {
        c = 1;
        w[i] = 0;
      }
    }
  }
  return c;
}
int ans = 0;
bool has[N];
bool dfs2(int x, int fa) {
  bool in_has = has[x];
  for (int i = head[x]; i; i = nxt[i]) {
    int v = to[i];
    if (v != fa) {
      bool tmp = dfs2(v, x);
      if (tmp) {
        in_has = tmp;
        // clog << x << "=>" << v << endl;
        ans += 2 * w[i];
        w[i] = 0;
      }
    }
  }
  return in_has;
}
int main() {
  t = read();
  while (t--) {
    ans = 0;
    ecnt = 0;
    memset(head, 0, sizeof head);
    memset(has, 0, sizeof has);
    memset(nxt, 0, sizeof nxt);
    n = read(), k = read(), x = read(), y = read();
    for (int i = 1; i <= k; i++) {
      a[i] = read();
      has[a[i]] = 1;
    }
    for (int i = 1; i < n; i++) {
      int a = read(), b = read();
      adde(a, b);
    }
    memset(dis, 0, sizeof dis);
    dfs1(x, 0);
    ans += dis[y];
    dfs2(x, 0);
    cout << ans << endl;
    // clog << endl;
  }
  // TODO: code here
  return 0;
}
```

# 1282 B2. K for the Price of One (Hard Version)

[题面链接](https://codeforces.com/contest/1282/problem/B2) (Rating:1600)

> 很好的类背包思维题。

## Problem

$n$个物品，每个物品价格为$a_i$，每次买物品可以有两种选择：

- 花费$a_i$购买$i$。
- 花费$a_i$购买**恰好**$k$个价格小于等于$a_i$的物品。

现在有$q$元，求最多可以买多少物品。

## Solution

首先肯定要买最便宜的物品。
可以想到的是，每次枚举正常选择前$i$个物品，后$i$个物品$k$个$k$个的买。进一步研究规律发现，只需要枚举前$k$个物品即可，因为多于$k$个物品必定可以通过组成一组来取得最小代价。

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
const int N = 2E5 + 10;
int a[N];
int sum[N];
int t, n, p, k;
void init() {
  n = read(), p = read(), k = read();
  for (int i = 1; i <= n; i++) {
    cin >> a[i];
  }
  sort(a + 1, a + 1 + n);
  for (int i = 1; i <= n; i++) {
    // clog << a[i] << " \n"[i == n];
    sum[i] = sum[i - 1] + a[i];
  }
}
int main() {
  int t = read();
  while (t--) {
    init();
    int ans = 0;
    for (int i = 0; i <= k; i++) {
      // clog << "Checking [" << 1 << "<<" << i << "]" << endl;
      if (sum[i] > p) {
        break;
      }
      int nans = i;
      int tmpp = p - sum[i];
      for (int j = i + 1; j <= n; j += k) {
        int endp = j + k - 1;
        if (endp > n) {
          break;
        }
        // clog << "Checking [" << j << "<<" << endp << "]" << endl;
        if (a[endp] <= tmpp) {
          nans += k;
          tmpp -= a[endp];
        } else
          break;
      }
      // clog << "Answer=" << nans << endl;
      ans = max(nans, ans);
    }
    cout << ans << endl;
  }
  // TODO: code here
  return 0;
}
```
# 1681 D. Required Length
[题面链接](https://codeforces.com/contest/1681/problem/D) (Rating:1700)
> 数位DP？食我BFS！
## Problem
给出两个数$n,k$，其中$2\le n \le 19,1\le k\le10^{n-1}$。
每次可以选择$k$中任意一位上的数$k_i$，把$k$变成$k\times k_i$。
求最少几次操作能把

## Solution
考虑BFS转移，用map记下步数即可。
## Code
```C++
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
int n, k;
signed main() {
  n = read(), k = read();
  char maxc = 0;
  for (auto s : to_string(k)) {
    maxc = max(s, maxc);
  }
  if (maxc == '1' && n > to_string(k).length()) {
    cout << -1 << endl;
    return 0;
  }
  map<int, int> dist;
  queue<int> q;
  q.push(k);
  dist[k] = 0;
  while (!q.empty()) {
    auto k = q.front();
    q.pop();
    string s = to_string(k);
    if (s.length() == n) {
      cout << dist[k] << endl;
      return 0;
    }
    for (auto c : s) {
      int i = c - '0';
      int nxt = k * i;
      if (!dist.count(nxt)) {
        dist.insert(make_pair(nxt, dist[k] + 1));
        q.push(nxt);
      }
    }
  }
  // TODO: code here
  return 0;
}
```

