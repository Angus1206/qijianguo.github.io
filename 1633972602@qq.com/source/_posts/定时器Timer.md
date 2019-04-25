---
title: 对象及变量的并发访问
categories: Java多线程编程核心技术
tags: 多线程
time: 2019-04-24 10:41:41
---

在JDK库中Timer类主要负责计划任务的功能，也就是在指定的时间开始执行某一任务。

Timer类主要设置计划任务，但封装任务的却是TimerTask类。执行计划任务的代码要放到TimerTask的子类中，因为TimerTask是一个抽象类。

创建Timer类就是启动一个新线程，但它不是守护线程，它一直在运行。
# 方法schedule(TimerTask task，Date date)的测试

# 方法schedule(TimerTask task, Date firstTime, long period)的测试

# 方法schedule(TimerTask task, long delay)的测试

# 方法schedule(TimerTask task, long delay, long period)的测试

# 方法scheduleAtFixedRate(TimerTask task, Date firstTime, long period)的测试
