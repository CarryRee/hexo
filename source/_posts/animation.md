---
title: CABasicAnimation 内存不释放问题
date: 2017-07-21 17:01:53
tags: 动画 内存管理
categories: 
---
CABasicAnimation 内存不释放问题
--

最近在项目遇到一个问题就是，在页面dismissViewController后发现该页面没有被释放，仔细查阅后，发现页面用了CABasicAnimation的removedOnCompletion = NO会导致不被释放，应该在dismissViewController之前调用removeAnimationForKey

- - -

**原因**：
removedOnCompletion 默认为YES，代表动画执行完毕后就从图层上移除，图形会恢复到动画执行前的状态。如果想让图层保持显示动画执行后的状态,那就设置为NO，也就是说图层一直在从而导致图层未释放。

**解决**：
在界面结束之前先手动调用removeAnimationForKey


