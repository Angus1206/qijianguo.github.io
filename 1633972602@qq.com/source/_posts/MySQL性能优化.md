---
title: MySQL性能优化
categories: MySQL
tags: mysql
date: 2018-12-29 18:30:50
---


* 【很实用】mysql慢查询 https://www.cnblogs.com/sunke/p/5779230.html
<!-- more -->
> 
> SHOW VARIABLES LIKE '%slow%'
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181116173750352.png)
> SET  @@global.slow_query_log = ON;
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181116173822632.png)
> SET @@global.log_output='TABLE';
> 可以查看慢查询的语句，包含慢查询的SQL、执行时间、频率等
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181116173915569.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)
> SET GLOBAL long_query_time = 2
> 设置大于long_query_time秒的查询
模拟一下
> SELECT SLEEP(10);



*【很实用】MySQL优化技巧：https://blog.csdn.net/u013087513/article/details/77899412
