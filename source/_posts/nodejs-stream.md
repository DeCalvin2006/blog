---
title: lghelper开发——番外篇1：神奇的Stream
tags:
  - Node.js
  - Typescript
  - Javascript
  - lghelper开发
categories:
  - [Node.js]
  - [lghelper开发, 番外]
date: 2022-08-13 16:08:33
---

在开发`lghelper`的过程中，从洛谷获取数据这一过程必不可少。获取数据必然需要 HTTP 请求，我这里就选择了使用自带的`https`库中的`request`方法。可是对 Node 标准库一无所知的我毫无意外地出了错。

<!-- more-->

# 起因

在进行测试时，我发现有时程序会报错退出，错误输出大概如下：

```plaintext
P1001
Parsing P1001...

undefined:1
{"code":200,"currentTemplate":"ProblemShow","currentData":{"problem":{"background":"\u5f3a\u70c8\u63a8\u8350[\u65b0\u7528\u6237\u5fc5\u8bfb\u5e16](\/discuss\/show\/241461)\u3002\n\n**\u4e0d\u719f\u6089\u7b97\u6cd5\u7ade\u8d5b\u7684\u9009\u624b\u8bf7\u770b\u8fd9\u91cc\uff1a**\n\n\u7b97\u6cd5\u7ade\u8d5b\u4e2d\u8981\u6c42\u7684\u8f93\u51fa\u683c\u5f0f\u4e2d\uff0c**\u4e0d\u80fd\u6709\u591a\u4f59\u7684\u5185\u5bb9**\uff0c**\u8fd9\u4e5f\u5305\u62ec\u4e86\u201c\u8bf7\u8f93\u5165\u6574\u6570 $\\bm a$ \u548c $\\bm b$\u201d \u8fd9\u4e00\u7c7b\u7684\u63d0\u793a\u75

SyntaxError: Unexpected end of JSON input
    at JSON.parse (<anonymous>)
    at IncomingMessage.<anonymous> (/home/decalvin/lghelper/dist/parse.js:17:31)
    at IncomingMessage.emit (node:events:527:28)
    at IncomingMessage.Readable.read (node:internal/streams/readable:527:10)
    at flow (node:internal/streams/readable:1011:34)
    at resume_ (node:internal/streams/readable:992:3)
    at processTicksAndRejections (node:internal/process/task_queues:83:21)
```

大概的意思就是，`JSON.parse()`接受到了不合法的输入。上方的输出也可以看出，数据并不完整，只有大约一半。我觉得程序写的好像没问题啊，改动了两处觉得有些问题的地方就又测试了一遍，这个 BUG 又消失了。但当我写完另一个功能模块时再次测试时，却发现这个 BUG 神奇的又出现了。

# Debug！

通过查找网络上的相关文档，我搞清楚了为什么会出现这种情况。
当时相关部分的代码如下：

```ts
const req = request(options, (res) => {
  res.on("data", (d: Buffer) => {
    parseProblem(
      (JSON.parse(d.toString()) as ProblemResult).currentData.problem
    );
  });
});
req.on("error", (err) => {
  console.error("Error!");
  console.error(err);
  exit(1);
});

req.end();
```

问题出在哪里呢？`res`是一个`Stream`类。

## `Stream`类的特性

`Stream`类正如其名，是流读取。`Stream`类型有两种读取模式：一种是`paused`模式，就像 C++中的`cin`；另一种是`flowing`模式，一块一块地读取数据，然后一块一块地被处理：但是非常危险的是，如果不监听流，数据很可能会丢失，因为数据是流动的。
看到这里你大概明白为什么会出现数据丢失的情况了吧！没错，就是因为我们只监听到了一部分数据。详细一点就是我们在数据块的中间监听到了不完整的数据，然后直接扔给了`JSON.parse()`，就有了这样的问题。

## 修改

修改其实也很简单，就是加上一个缓冲，把读到的数据缓存到字符串里面。当`Stream`中所有的数据都传输完成之后（也就是`close`事件），我们把整个缓存丢给`JSON.parse()`就不会有事了。

# 后记

其实用 Node.js 开发确实有不小的难度，毕竟事件驱动的语言和命令行程序几乎完全不相干。但是既然选择了做这件事，那就坚持不懈地干下去，学习新的东西。
