---
title: Windows 关闭端口号
categories: winsw
tags: winsw
---

查询端口号占用的进程

<!-- more -->

```
C:\Users\Administrator>netstat -ano | findstr 8080
  TCP    0.0.0.0:8080           0.0.0.0:0              LISTENING       9172
  TCP    192.168.1.160:60855    47.92.114.119:8080     ESTABLISHED     9172
  TCP    [::]:8080              [::]:0                 LISTENING       9172
```

9172就是进程号

关闭进程
```
C:\Users\Administrator>taskkill -PID 9172 -F
成功: 已终止 PID 为 9172 的进程。
```

