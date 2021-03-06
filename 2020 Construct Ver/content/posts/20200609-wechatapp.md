---
title: "小程序开发笔记：我是欧皇吗"
date: 2020-06-09T00:00:00+09:00
hidden: false
draft: false
tags: ["开发笔记"]
summary: "因为疫情在家没有事情做，决定用四十八小时挑战制作一个微信小程序。这个小程序用于计算各种扭蛋游戏的结果概率，在平常玩各种手游中具有参考价值和使用价值。目前该小程序已登陆微信小程序商店，搜索“我今天是欧皇吗”即可找到。"
author: "Chuanbo"
count: "1998 Words"
---

这是 48h 从零开始的微信小程序挑战的成果。可以在 [GitHub](https://github.com/cbhua/wxapp-am-i-lucky) 上看到这个项目。

这个挑战开始得很曲折，是这样的：最近在家的空闲时间比较多，想着自己开发个游戏，开个大坑，毕竟做游戏这个事情我从好久之前就在想了。选定了 Unity 框架之后发现 Premium 可以用 GitHub Education 认证，于是我就去找 GitHub，提交了学生证扫描件之后需要两三天才能出结果，在这两三天时间里不知道干什么，就搞个微信小程序吧。

所以，我尝试在 GitHub Education 过审前把这个坑填上。

说实话我个人很不喜欢微信，但是 Share 一个工具的几种途径：APP（Android 也不是不行，不过我从来不用 Android 所以没什么偏好，同时我又没办法开发 iOS）、网页（我的 ECS 刚到期），而且反正是瞎做着玩，就试试微信小程序吧，毕竟最近这么火。

稍微扫了一眼 Hello World 文档，就开始写程序了。请不要这么做，之后踩的坑多都是因为太莽撞地开始。

微信小程序的框架很好懂，写起来和网页差不多：HTML + CSS + JS，只是多了一些额外的配置数据和微信自带函数的调用，搜一搜，看看文档都可以做到。

《我是欧皇吗》这个小程序的功能很简单，就是计算抽卡游戏涉及的概率计算，这点在 GitHub README 上面有介绍，或者打开小程序自然就可以懂了，这里就不提了。

因为功能很简单，所以 JS 写起来也非常简单，大概 10 分钟就可以解决的程度，其本质就一个包含组合的算式：
$$
C_{gachaTimes}^{winTimes}\times probability^{winTimes}\times (1-probability)^{gachaTimes-winTimes}
$$
即：
$$
\frac{gachaTimes!}{winTimes!\times (gachaTimes-winTimes)!}\times probability^{winTimes}\times (1-probability)^{gachaTimes-winTimes}
$$
其中 `gachaTimes` 即抽卡的次数，`winTimes` 即出货的次数，`probability` 即出货概率。

只算算概率的话这个小程序也太无聊了一点，因为最近经常和同学打日麻，对各种役种的和率有了一些认识，就去天凤上搜了搜有相关的和率统计，所以把各种日麻和率写在对应概率范围内做参照，丰富一下内容。

作为一个对日麻概率和对抽卡概率都有概念的人，不得不说一些对照结果还是挺出乎意料的，虽然知道抽卡全不中的概率不低，但是没想到高到和我和一次断幺九差不多，这也算达到了我增加这一部分的效果。

核心写完了，剩下的就是补全其他的东西了，数据需要输入，于是最烦人的正则检查就有必要了。

需要考虑的情况包括：填写的出货次数比抽卡次数还要多，填写的抽卡次数太高而超过了微信小程序的计算范围，导致 return 一个 `NaN`，或者是概率实在太低，微信小程序的最小浮点数是 `1e-10` 左右，比这个更小就变成了 0。这些情况都需要考虑对应的输出。

不得不说刚开始写这个的时候完全没有考虑到鲁棒性检查这么复杂，所以导致后面改的时候已经乱成了一团，现在想来，如果在设计的时候考虑好过大、过小、错误输入的情况，直接划分四块条件渲染就可以了。

但是当时确实没有想这么多，在一个渲染块里面各种混乱的 `if` 判断给我后来改代码带来了太多的麻烦。

不管怎样，鲁棒性检查这部分弄完了，就该做页面间通讯部分了。因为结果输出在 `Result Page`，而输入数据在 `Cal Page`。但是在最开始的时候我因为没有仔细阅读文档，对微信小程序的 `onLoad` 和 `onShow` 等几个函数没有搞清楚触发时间，导致直传的数据一直得不到刷新。虽然问题很容易就解决了，但是因为没好好读文档浪费了不少时间。（毕竟这么基础的东西没有不读的道理）

数据传完了，整个工具就可以用了，之后绝大部分时间是在设计 CSS。不得不说微信小程序的 CSS 设计很不方便，缺少了类似于 Chrome 中好用的 Inspector，这样在设置 margin, padding 或者 location 的时候都需要手动给每个块添加黑色边框，以方便定位。

我平常用 CSS 很少，模块定位很不熟练，就将就着按照比例摆了摆，测试了几个主流的手机分辨率都没有乱版的情况就放那了，算是歪歪扭扭地把 CSS 拼好了。

不过抛开定位的问题，我的按钮和艺术字的设计还是不错的。

CSS 弄完，设计了一个 icon 就到了第二天晚上了。说实话，到现在为止我还没有读微信的小程序审核手册，甚至压根就没考虑过这个小程序会不会过审。于是随便写写简介，传了个头像就提交审核了，过两天看看结果怎么样。

提交审核之后会想起这两天搞得东西，简直乱七八糟，总结起来就是有这几个问题：

- 微信小程序的文档基础部分一定要通读，否则就像页面生命周期函数顺序搞不清楚耽误大量的时间，还要重构代码；一些基础的功能微信已经给出了实现方法，照抄就可以；
- 小程序开发的问题搜索条目数不是很多，毕竟专门玩小程序的人也不是那么多，有很多问题还是要自己动手读文档去解决的；
- 开发程序要遵循既有的开发流程规划，比如规划功能、分析需求、列举限制、框架设计，在此之后再动手去写，会高效很多，还能避免很多重构；

不过不这么被坑一次我说不一定还不理解上面的内容，所以两天时间的代价试错几个经验还是很值的。完成了基本的入门，之后如果还想进一步做小程序就读读其他人的源码学习学习，慢慢来。

不过之后应该大概率去玩 Unity 去了，下次小程序开发遥遥无期。