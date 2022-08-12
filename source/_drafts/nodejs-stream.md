---
title: lghelper开发——番外篇1：神奇的Stream
tags: 
	- Node.js
	- Typescript
	- Javascript
categories:
	- [Node.js]
	- [lghelper开发，番外]
---
在开发`lghelper`的过程中，从洛谷获取数据这一过程必不可少。获取数据必然需要HTTP请求，我这里就选择了使用自带的`https`库中的`request`方法。可是对Node标准库一无所知的我毫无意外地出了错。

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

大概的意思就是，`JSON.parse`接受到了不合法的输入。上方的输出也可以看出，数据并不完整，只有大约一半。我觉得程序写的好像没问题啊，改动了两处觉得有些问题的地方就又测试了一遍，这个BUG又消失了。但当我写完另一个功能模块时再次测试时，却发现这个