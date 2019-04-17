---
title: Another app is currently holding the yum lock; waiting for it to exit...
categories: Linux
tags: linux
---

* yum 安装时出现如下错误:

```
[root@localhost gitrepo]#  yum install gitlab-ce
Loaded plugins: fastestmirror, langpacks
Existing lock /var/run/yum.pid: another copy is running as pid 6807.
Another app is currently holding the yum lock; waiting for it to exit...
  The other application is: PackageKit
    Memory : 120 M RSS (532 MB VSZ)
    Started: Tue Oct  9 18:18:18 2018 - 18:17 ago
    State  : Sleeping, pid: 6807
Another app is currently holding the yum lock; waiting for it to exit...
  The other application is: PackageKit
    Memory : 120 M RSS (532 MB VSZ)
    Started: Tue Oct  9 18:18:18 2018 - 18:19 ago
    State  : Sleeping, pid: 6807
Another app is currently holding the yum lock; waiting for it to exit...
  The other application is: PackageKit
    Memory : 120 M RSS (532 MB VSZ)
    Started: Tue Oct  9 18:18:18 2018 - 18:21 ago
    State  : Sleeping, pid: 6807
```

* 原因: yum只能支持一个例程运行，所以如果有一个例程已经在运行，其他的必须等待该进程退出释放lock。出现这种情况时，可以用以下命令来恢复：

```
rm -f /var/run/yum.pid
```

