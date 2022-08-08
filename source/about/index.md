---
title: About
date: 2022-07-04 11:05:24
layout: about
---

# 关于我

## 个人信息

准高一 Oier，坐标 SD，即将进入 SDLYYZ 就读。
联系方式在上面可以找到。

## 获奖记录

2018 NOIP 小学组一等
2019 CSP-J 二等
2020 CSP-J 二等
2021 CSP-S 二等
2021 NOIP 二等
To be continued......

```cpp
/*
 * Author : $%U%$
 * Date : $%Y%$-$%M%$-$%D%$ $%h%$:$%m%$:$%s%$
 * */

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
int main() {
  // TODO: code here
  return 0;
}
```
