---
title: Git修改已经提交的注释或日志
categories: Git
tags: git
---

<!-- more -->

转自：http://www.cnblogs.com/tocy/p/git_commit_log_modify.html

> 对于已经修改提交过的注释，如果需要修改，可以借助 git commit --amend 来进行。（注意：修改日志的功能在svn中需要server支持，在git中可以独立修改）
注意：必须是最近提交，还没有push的注释。	

例如，在base 里最新的提交就是 ***resolving the crash problem***，现在需要将其改为resolving the ***crash problem to fix bug xxx***
在base目录下，输入 ***git commit --amend***，就会进入一个文本编辑界面（如下），在注释的地方修改 ，保存然后退出，这样注释就修改了，再重新 ***push.***

```
resolving the crash problem

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# Date:      Sat Dec 12 08:44:59 2015 +0800
#
# On branch master
# Your branch and 'origin/master' have diverged,
# and have 1 and 1 different commit each, respectively.
#   (use "git pull" to merge the remote branch into yours)
#
# Changes to be committed:
#       modified:   c_c++/1-c-invoke-cpp-main.c
#       modified:   c_c++/1-c-invoke-cpp.cpp
#
```
注意以上界面使用vi操作习惯。
