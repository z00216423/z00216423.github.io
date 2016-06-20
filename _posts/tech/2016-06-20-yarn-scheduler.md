---
layout: post
title: YARN的资源调度器
category: 技术
tags: blog
---

我们知道YARN是一个典型的双层调度框架，第一层调度负责对不同应用框架的资源分配与调度，主要是由调度器来实现；第二层调度是应用内的任务调度与管理，这部分职责交给Application Master来实现。目前，YARN中有三种调度可选择： `FIFO Scheduler`、`Capaticity Scheduler`、`Fair Scheduler`。

### FIFO Scheduler
`FIFO Scheduler`是最简单的调度，只能应用于单队列应用的调度，所有应用安提交时间先后顺序排成队列，按照先进先出原则进行资源分配。只有分配完先提交应用需求后，才能开始下一个应用的资源分配。优点是，简单易实现，不需要任何配置。缺点也很明显，