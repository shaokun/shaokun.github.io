---
layout: post
title:  "编程随想：Objects on Rails"
cover:  cover.jpg
date:   2013-12-26 16:05:26
categories: 编程随想
---

最近不时挤时间在看[Objects on Rails][objects-on-rails]，联想到DHH的[SJR策略][sjr]、[Concerns][concerns]。SJR是对Single Page App的反驳，Concerns则是在反对过度OOP。Rails是Web开发最优实践的集大成者，在一定程度上我越发赞成DHH的观点（我曾经很赞同，然后很怀疑）。

不过大家也要看到DHH也提到他们在某些Feature也会采用高度的JSON+JS结构，我猜他们项目中的某些地方也会朝着OOP的极端优化，而不是简单的迎合Rails的MVC。Objects on Rails的作者其实也不时说在实际项目中他可能不会使用如此高度抽象跟解耦的策略。

不过无论如何，在向高阶进步的过程中，你始终要经历各种阵痛，而程序开发的最大难度之一是开发进度与完美程度的平衡。我想DHH在这里找到了更好的平衡吧，这点与他个人一直在讽刺某些技术的观点相关，也值得我们去思考。当然，要成为更好的rails工程师，始终不是局限于简单使用rails框架，或者说rails其实是给优秀程序员使用的，懂得如何去权衡会更好用。推荐阅读Objects on Rails。

[objects-on-rails]: http://objectsonrails.com
[sjr]: https://37signals.com/svn/posts/3697-server-generated-javascript-responses
[concerns]: http://37signals.com/svn/posts/3372-put-chubby-models-on-a-diet-with-concerns