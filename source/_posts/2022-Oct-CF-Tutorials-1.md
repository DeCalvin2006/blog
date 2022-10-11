---
title: 10月CF做题的一些题解①:1107D,1555D,1032C
tags:
  - Codeforces
  - 题解
categories:
  - CF题解合集
date: 2022-10-11 10:45:49
---

<!-- more -->

# 1107 D. Compression

> 非常有意思的二维前缀和。

[题面链接](https://codeforces.com/contest/1107/problem/D) (Rating:1800)

## Problem

给出一个$n\times n$的$0-1$矩阵$A$，求他的的压缩矩阵$B$，使得$B$是一个$x\times x$的矩阵（$x\mid n$），且对于所有的$1\le i,j \le n$，有
$$A[i][j]=B[\lceil\frac{i}{x}\rceil][\lceil\frac{j}{x}\rceil]$$
求最大的$x$。

## Solution

不难发现（其实做了好久才发现），上面式子的含义就是，把矩阵分成$x\times x$大小的块，使得这里面的块的数相同。由于他是一个$0-1$矩阵，所以很明显可以看出他的规律：每一个块的数之和要么是$x^2$，要么是$0$。二维前缀和即可。

二维前缀和公式（容斥原理）：

$$
\begin{align}
& S[i][j]=S[i-1][j]+S[i][j-1]-S[i-1][j-1]+A[i][j]\\
& sum((x_1,y_1),(x_2,y_2))=S[x_2][y_2]-S[x_2][y_1-1]-S[x_1-1][y_2]+S[x_1-1][y_1-1]
\end{align}
$$

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
const int N = 5200 + 10;
int n;
bool k[N][N];
int s[N][N];
int calcsum(int x1, int y1, int x2, int y2) {
  return s[x2][y2] - s[x1 - 1][y2] - s[x2][y1 - 1] + s[x1 - 1][y1 - 1];
}
int main() {
  n = read();
  // TODO: code here
  for (int i = 1; i <= n; i++) {
    string tmp;
    cin >> tmp;
    for (int j = 0; j < n / 4; j++) {
      int now = tmp[j];
      if ('0' <= now && now <= '9') {
        now -= '0';
      } else {
        now = 10 + now - 'A';
      }
      for (int p = 3; p >= 0; p--) {
        k[i][j * 4 + 3 - p + 1] = now & (1 << p);
      }
    }
  }
  // for (int i = 1; i <= n; i++)
  //   for (int j = 1; j <= n; j++)
  //     clog << k[i][j] << " \n"[j == n];
  for (int i = 1; i <= n; i++) {
    for (int j = 1; j <= n; j++) {
      s[i][j] = s[i - 1][j] + s[i][j - 1] - s[i - 1][j - 1] + k[i][j];
    }
  }
  // for (int i = 1; i <= n; i++)
  //   for (int j = 1; j <= n; j++)
  //     clog << s[i][j] << " \n"[j == n];
  for (int x = n; x >= 1; x--) {
    if (n % x != 0)
      continue;
    for (int i = 1; i <= n - x + 1; i += x) {
      for (int j = 1; j <= n - x + 1; j += x) {
        // cout << i << ' ' << j << ' ' << i + x - 1 << ' ' << j + x - 1 <<
        // endl;
        if (calcsum(i, j, i + x - 1, j + x - 1) != 0 &&
            calcsum(i, j, i + x - 1, j + x - 1) != x * x) {
          goto notok;
        }
      }
    }
    cout << x << endl;
    break;
  notok:
    continue;
  }
  return 0;
}
```

# 1555 D. Say No to Palindromes

> 精心构造。

[题面链接](https://codeforces.com/contest/1555/problem/D) (Rating:1600)

## Problem

给定一个仅包含$a,b,c$三种字母的字符串$s$，给定$m$个区间$[l,r]$，求最少改变多少个字母，使得不存在长度大于等于$2$的回文子串。

## Solution

容易得出$s_{i-1}\neq s_i\neq s_{i+1}$。又由于只有三个字母，那么可以得到$s_i=s_{i+3}$。
所以最后满足的形式一定是$(\text{a,b,c的排列})^k$。
$a,b,c$的排列只有 6 种，对于每一种方式，预处理一个前缀和即可。

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
const int N = 2e5 + 10;
int n, m;
char buf[N];
int a[N];
const char *cstr[] = {
    "abc", "bca", "cab",

    "acb", "cba", "bac",
};
int pr[6][N];
int main() {
  n = read(), m = read();
  cin >> (buf + 1);
  for (int i = 0; i < 6; i++) {
    for (int j = 1; j <= n; j++) {
      // cout << cstr[i][j % 3];
      if (buf[j] != cstr[i][j % 3]) {
        pr[i][j] = pr[i][j - 1] + 1;
      } else
        pr[i][j] = pr[i][j - 1];
    }
  }
  for (int i = 1; i <= m; i++) {
    int l = read(), r = read();
    int ans = INT_MAX;
    for (int i = 0; i < 6; i++) {
      ans = min(ans, pr[i][r] - pr[i][l - 1]);
    }
    cout << ans << endl;
  }
  return 0;
}
```

# 1032 C. Playing Piano

> 记忆化搜索。

[题面链接](https://codeforces.com/contest/1032/problem/C) (Rating:1700)

## Problem

给定一个序列 a，构造一个仅包含$1-5$的序列 b，使得：

- $a_{i}<a_{i+1}\Rightarrow b_{i} < b_{i+1}$
- $a_{i} > a_{i+1} \Rightarrow b_{i} > b_{i+1}$
- $a_{i} = a_{i+1}\Rightarrow b_{i} \neq b_{i+1}$

不存在输出$-1$。

## Solution

正常搜索即可，记得记忆化不然会 TLE。

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
int a[N];
int n;
int ans[N], cnt = 0;
bool dp[N][6];
bool dfs(int x, int f) {
  if (dp[x][f] == 0)
    return 0;
  if (x > n) {
    return 1;
  }
  if (a[x] > a[x - 1]) {
    for (int i = f + 1; i <= 5; i++) {
      if (dfs(x + 1, i)) {
        ans[x] = i;
        return 1;
      }
    }
  } else if (a[x] < a[x - 1]) {
    for (int i = 1; i < f; i++) {
      if (dfs(x + 1, i)) {
        ans[x] = i;
        return 1;
      }
    }
  } else {
    for (int i = 1; i <= 5; i++) {
      if (i == f)
        continue;
      if (dfs(x + 1, i)) {
        ans[x] = i;
        return 1;
      }
    }
  }
  return dp[x][f] = 0;
}
int main() {
  n = read();
  for (int i = 1; i <= n; i++) {
    a[i] = read();
  }
  memset(dp, 1, sizeof dp);
  bool ok = dfs(1, 0);
  if (ok) {
    for (int i = 1; i <= n; i++) {
      cout << ans[i] << ' ';
    }
    cout << endl;
  } else
    cout << "-1" << endl;
  return 0;
}
```
