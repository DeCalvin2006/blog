---
title: 最近CF做题的一些题解③:1084C,1703F,797B
tags:
  - Codeforces
  - 题解
  - DP
categories:
  - CF题解合集
date: 2022-09-26 14:53:41
---

这是 Codeforces 题解系列的第 ③ 篇。

<!-- more -->

# 1084 C. The Fair Nut and String

[题面链接](https://codeforces.com/contest/1084/problem/C) (Rating:1500)

> 字符串，排列组合。

## Problem

给出一段小写字母组成的字符串 s。
对于一段序列$p_1,p_2,p_3\dots p_k$，给出如下限制条件。

- $\forall p_i(i\in[1,k]),a_{p_i}=\tt{'a'}$ 。
- $\forall p_i(1\le l<k),\exists j\in[p_i,p_{i+1}]\text{且}a_j=\tt{'b'}$

求有多少序列$p$满足条件。

## Solution

首先可以忽略所有的$\mathtt{'a'}$,$\mathtt{'b'}$之外的字符。然后，将相同的字符进行合并。忽略前导$\mathtt{'b'}$，因为一定不会被包含。现在$\mathtt{'b'}$之间的$\mathtt{'a'}$就被分割成多个集合了。答案就是

$$
\prod_{1\le i\le k}{\text{第}i \text{段}\mathtt{'a'}\text{的个数}+1}-1
$$

```C++
#include <bits/stdc++.h>
#define int long long
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
const int MOD = 1E9 + 7;
string s;
int cnt = 1;
int ans[N];
int mul[N];
signed main() {
  // TODO: code here
  char c = '\0', last = '\0';
  while ((c = getchar()) != '\n') {
    if (c == 'a') {
      ans[cnt]++;
      last = c;
    }
    if (c == 'b' && last != c && cnt[ans] != 0) {
      cnt++;
      last = c;
    }
  }
  if (ans[cnt] == 0)
    cnt--;
  mul[0] = 1;
  for (int i = 1; i <= cnt; i++) {
    mul[i] = mul[i - 1] * (ans[i] + 1) % MOD;
  }
  cout << mul[cnt] - 1 << endl;
  return 0;
}
```

# 1703 F. Yet Another Problem About Pairs Satisfying an Inequality

[题面链接](https://codeforces.com/contest/1703/problem/F) (Rating:1300)

> 神奇的二分。

## Problem

给出一个序列$a_1,a_2,a_3,\dots a_n$，求满足$a_i<i<a_j<j$的$(i,j)$数对个数。

## Solution

首先可以拆分为$a_i<i$和$a_j<j$，丢掉不满足的元素。然后对剩下的数组按照序列值大小进行排序。在序列中二分每个索引的位置即可。

```C++
#include <bits/stdc++.h>
using namespace std;
#define int long long
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
int n;
int t;
struct number {
  int val, p;
} a[N];
signed main() {
  t = read();
  while (t--) {
    n = read();
    int cnt = 0;
    for (int i = 1; i <= n; i++) {
      a[0].val = read();
      if (a[0].val < i) {
        a[++cnt].val = a[0].val;
        a[cnt].p = i;
      }
    }
    n = cnt;
    if (n == 0) {
      cout << 0 << endl;
      continue;
    }
    int ans = 0;
    stable_sort(a + 1, a + 1 + n,
                [](number a, number b) { return a.val < b.val; });
    for (int i = 1; i <= n; i++) {
      // clog << a[i].p << ":" << a[i].val << endl;
      int an = upper_bound(a + 1, a + 1 + n, a[i].p,
                           [](int a, number b) { return a < b.val; }) -
               a;
      ans += n - an + 1;
      // clog << an << endl;
    }
    cout << ans << endl;
  }
  return 0;
}
```

# 797 B. Odd sum

[题面链接](https://codeforces.com/contest/797/problem/B) (Rating:1400)

> 构造。构造。构造。

## Problem

求最大和，使得这个和是个奇数。

## Solution

先检查最大和是不是奇数。如果不是奇数那就去掉一个最小的奇数，或者加上一个最大的负奇数。

## Code

```C++
#include <algorithm>
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
int a[N];
int maxsum[2];
int main() {
  n = read();
  int sum = 0;
  for (int i = 1; i <= n; i++) {
    a[i] = read();
    if (a[i] > 0) {
      sum += a[i];
    }
  }
  if (sum % 2 == 1) {
    cout << sum << endl;
  } else {
    int minpos = 1E9 + 7, maxneg = -1E9 - 7;
    for (int i = 1; i <= n; i++) {
      if ((a[i] % 2 + 2) % 2 == 1) {
        if (a[i] > 0) {
          minpos = min(minpos, a[i]);
        } else if (a[i] < 0) {
          maxneg = max(maxneg, a[i]);
        }
      }
    }
    cout << sum - min(minpos, -maxneg);
  }

  return 0;
}
```
