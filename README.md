---
title: 缓存
date: 2018-5-23 20:34:56
categories:
- 前端综合
tags:
- 前端综合
---

### 原文地址

https://www.mnot.net/cache_docs/

### 缓存是什么

缓存监视着发送的请求，并保存回复的备份————诸如HTML页面、图片和文件。当再次请求相同的URL时，从这些回复中而不是从服务器中获取它们。

### 为什么要用缓存

1. 降低延迟————因为这占用更少时间，使得Web页面更具响应式。

2. 减少网络传输————因为回复被重复利用，故而减少了带宽，节省了开支，也保持着较低的带宽需求并更易于管理。

### 有哪些类型的缓存

#### 浏览器缓存

您在任何现代浏览器的首选项对话框都能看到“缓存”选项。这允许您使用电脑的一部分硬盘存储回复信息。浏览器缓存用相当简单的规则工作。它会确保回复对于一次会话而言是新鲜的。

这些缓存在用户点击“后退”按钮或者链接时会很有用。或者，当您在整个站点使用相同的导航图片时，它们几乎即刻可以从浏览器缓存中获取。

#### 代理缓存

代理缓存用相同的规则工作，但规模更大。代理服务器用相同的方式为成百上千的用户提供服务。大公司和互联网服务提供商们经常在防火墙和独立设备（也叫中间人）上设置它们。

因为代理缓存并不是客户端或者服务器的一部分，而是留存于网络上，所以请求必须以某种方式路由到它们。一种方法是使用浏览器的代理设置手动告诉它使用哪一个代理。另一种方法则是使用拦截。拦截代理因为有底层网络本身重定向到的Web请求，所以客户端不需要进行配置，甚至不需要知道它们。

代理缓存是一种共享缓存。比起一个用户，它们同时让大量的用户使用它们，所以能极大地降低延迟和减少网络传输，因为流行的请求减少了大量的时间。

#### 网关缓存

也被称为"反向代理缓存"或"替代（surrogate）缓存"。网关缓存也是中间人，但并非由网络管理者而是网站管理者自己部署，为了让他们的网站有更好的可扩展性、可靠性和表现性。

请求可以通过多种方法路由到网关缓存，但是通常会使用负载均衡器来使得它们对于客户端来说更像是服务器。

内容分发网络（CDN）在整个网络中分发网关缓存并将之贩卖给感兴趣的站点。Speedera和Akamai是内容分发网络的典型案例。

本教程主要关注浏览器和网关缓存，当然其中一些信息也很适合那些对代理缓存感兴趣的人。

### Web缓存不适合我吗？我该怎么改进它们？

Web缓存是互联网中最容易被误解的技术。网站管理者尤其害怕失去对网站的控制，因为代理缓存可以“隐藏”他们的用户，使他们很难看到是谁在访问他们的网站。

不幸的是，如果Web缓存不存在，那么互联网上会有过多的变量来确保用户准确地看到他们的网站。如果感到担忧，本教程会教导您如何在不影响缓存的基础上获取统计信息。

另一个担忧则是缓存可能会提供过时或者陈旧的内容。不用担心，本教程也会向您展示如何配置服务器来控制内容的缓存。

从另一个角度说，如果您安排得当，缓存也可以让您的网站加载更快，并且节省了服务器和网络连接的负担。差异是很明显的：一个难以缓存的网站可能要耗时数秒去加载，然而一个利用了缓存的网站可以即刻看到内容。用户更青睐一个加载快的网站，并会更频繁地去访问它们。

让我们想一想，许多大型互联网公司都耗费了数百万美元去建设服务器来复制它们的内容，仅仅是为了让用户更快地访问它们。缓存也是如此，并且更接近于终端用户。最要命的是，您根本不用为它付钱。

有一个事实是，不管你喜欢与否，代理缓存和浏览器缓存都将存在。即使您不配置您的网站来正确地缓存，它们也会被缓存管理用用默认的方式缓存。

### Web缓存如何工作？

所有缓存都有一套规则来决定是否或何时从缓存中获取回复。有些规则被设置在协议里（HTTP 1.0 和 1.1），有些则通过缓存管理员（浏览器缓存的用户或者代理管理员）设置。

通常来说，以下是最常见的规则：

1. 如果回复头告诉缓存不要保存，那么就不会保存。
2. 如果请求已通过身份验证或者是安全的（比如HTTPS），那么不会通过共享缓存进行缓存。
3. 一个缓存的回复如果满足以下条件则被认为是新鲜的（也就是说可以被发送到客户端而不用经过服务器的检验）：
  * 它有一个超时时间或者时间控制头设置，并且还处于新鲜阶段。
  * 或者它最近已经获取到这个回复并且长时间未被修改。

  新鲜的回复会直接从缓存中被使用，无须通知服务器。
4. 如果回复陈旧，服务器则会验证它，或者告诉缓存这个备份是否仍然有效，
5. 在一些特定的情况下————比如说当离线时，一个缓存可以使用陈旧的回复，而无须通知服务器。

如果回复中没有验证项（ETag或Last-Modified头），并且它没有明确的新鲜度信息，那么它常常（但不总是）被认为是不可缓存的。

总之，新鲜度和验证是缓存对内容生效最重要的方式。一个新鲜的回复将立刻从缓存中被使用，而验证过的回复将避免重复发送整个回复如果它没被改变。
