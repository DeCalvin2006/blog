---
title: SYZOJ 源码解读 —— 项目依赖|技术栈
permalink: /syzoj/0-deps/
---

我们先来讲几个这些代码用到的东西。

<!-- more -->

文章不定时扩充。

## 前端

### Express

[Express](https://www.expressjs.com.cn/)是一个非常著名的 Node.js HTTP 服务器。同时他内置模板引擎的渲染接口，很方便我们使用 EJS/Pug/Jade（已经过时，请考虑使用 Pug 替代）等模板引擎进行渲染。本项目使用[EJS](https://ejs.bootcss.com/)。

{% note info %}

这里要提一嘴，Express 已经很老了，所以很多人强烈要求把他换掉，但是不可否认的是 Express 老当益壮，仍然有着无数像 SYZOJ 这样使用 Express+模板引擎 的网站。

{% endnote %}

### EJS

[EJS](https://ejs.bootcss.com/)（Embedding JS），顾名思义，可以让你在 HTML 语法中嵌入 JavaScript 语法（反过来说可能更有趣？）。Express 内建对 EJS 的支持。

## 通信

### JWT

JWT[^1]，全名 JSON Web Token，是身份验证和数据传输的一种解决方案，基于加密 Token 实现，同时也可以防止 CSRF 攻击。

SYZOJ 中 JWT 一般用来进行身份验证和请求

本项目中使用`jsonwebtoken`进行 JWT 相关的操作。

### WebSocket

WebSocket[^2]是一种通信协议，是为了解决轮询带来的高成本、低效率等问题而产生的新技术，同时能够实现 Server 端主动与 Client 端进行通信。

本项目使用 Socket.io 库处理 Websocket，主要的应用场景是事实返回提交结果。

## Misc

### Winston

[Winston](https://github.com/winstonjs/winston#readme)是一个非常流行的 Node.js 日志库，有很强的定制性。

### Lodash

[lodash](https://www.lodashjs.com/)可以看作是 Node.js 本地的 underscore（low-dash 的缩写:D），它提供了很多便捷的数据操作工具，可以让你的代码更加简洁，减少“造轮子”。

### Bluebird

[Bluebird](http://bluebirdjs.com/docs/getting-started.html)是一个 Node.js Promise 实现。

{% note info %}

**Promise 格式的异步编程**

Promise 的出现是为了解决“回调地狱”——四处嵌套的回调函数。Promise 用链式的`then`代替回调函数的嵌套，用`catch`替代回调函数中的错误处理，还可以用`all`和`race`实现调度，更简单地实现高性能的复杂任务。

Promise 的使用实例详见[这篇文章](https://zhuanlan.zhihu.com/p/27162908)。

{% endnote %}

[^1]: https://javaguide.cn/system-design/security/jwt-intro.html
[^2]: https://cloud.tencent.com/developer/article/1887095
