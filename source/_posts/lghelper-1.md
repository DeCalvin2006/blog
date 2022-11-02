---
title: lghelper开发日记1：万物之初
tags:
  - lghelper开发
  - Node.js
categories:
  - lghelper开发
date: 2022-08-13 10:27:58
---

受到[xalanq 的 cf-tool](https://github.com/xalanq/cf-tool/)启发，我深刻的意识到一个助手对于 OI 的作用之大。于是，看着~新建的文件夹~崭新的希望升起，我开始了 lghelper 的构思。

<!-- more-->

# 开始——JustForFun

说实话，想做一个洛谷助手并不是很容易。参考 cf-tool，巨大的代码量以及令人发指的代码水平，想要把项目做的既好用又好维护，我只能望洋兴叹啊。（然后你就自己摸了半个暑假的鱼是吧 XD）
最初其实想过很多方案：

- C++：C++我很熟悉，开发难度不是很大，但是唯一的问题就是跨平台。（我在 Linux 上面开发，但是不一定大家都用 Windows；我在 Windows 上开发，那我在 Linux 上面就要重写一版给自己用；搞适配又是很难的一件事。）
- Rust：Rust 跨平台，目前也有很多好用的包，开发出来的程序强度也有目共睹。但是我的水平不允许啊 QwQ。
- Python：最后考虑 Python 还是感觉无计可施了，因为我 Py 的寄术属实比上面两样差多了 QwQ。

# 出路

有一天我偶然看到了[competitive-companion](https://github.com/jmerle/competitive-companion)这个项目。这是一个浏览器插件，可以方便的获取浏览器中 OJ 上的数据、题目等信息，并通过端口转发给本地的编辑器/IDE。我突然想到，多年前曾看过一篇关于 Node.js 编写命令行程序的文章，于是就想到用 Node.js 写一个命令行程序。

# 开干！

现在项目经过开发已经上线。已经发布了同名 npm 包`lghelper`。
目前实现的功能只有获取样例和题面。

# 食用指南

1. 安装 npm 包`lghelper`。
2. 直接运行`lghelper`即可。配置文件为 JSON 格式，保存在用户主目录下`.lghelper`，默认保存在用户主目录下`.luogu/`文件夹中。
3. 运行`lghelper parse 题号`即可。

# 未来

未来的计划已经在[主页](https://github.com/DeCalvin2006/lghelper)上做了规划，打算在正式版放出之前先迭代 3 到 4 个版本，至少把最基础的功能做好吧。
