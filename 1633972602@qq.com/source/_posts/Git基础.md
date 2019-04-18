---
title: Git基础
categories: Git
tags: git
---

# 1.1 获取项目的git仓库
有两种取得git仓库的方法。
## 1.1.1 从当前目录初始化
```
# 创建项目文件夹
> mkdir  git_practice

<!-- more -->

# 进入文件夹下
> cd git_practice

# 初始化
> git init
```

## 1.1.2 从现有仓库克隆
语法

```
git clone [url]
```

自定义项目名称：只需要再 url 后自定义项目名
```
$ git clone [url]  mygrit
```

# 1.2 记录更新到仓库
工作区下的文件状态分为：未跟踪 / 已跟踪。 文件的状态变化周期如下：
![文件的状态变化周期](https://img-blog.csdnimg.cn/20190308154121432.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)
## 1.2.1 查看文件的状态

```
$ git status
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean

```
注：没有查找到跟踪着的文件。

用vim新建一个README

```
$ vi README

# 查看状态
$ git status
# On branch master
# Untracked files:
# (use "git add <file>..." to include in what will be committed)
#
# README
nothing added to commit but untracked files present (use "git add" to track)
```
注：untracked files表示未跟踪， 即不会被git跟踪。

## 1.2.2 跟踪新文件

```
$ git add README

$ git status
# On branch master
# Changes to be committed:
# (use "git reset HEAD <file>..." to unstage)
#
# new file: README
#
```
注：README 文件已被跟踪，并处于暂存状态。

“Changes to be committed” 这行下面的，就说明是已暂存状态。如果此时提交，那么该文件此
时此刻的版本将被留存在历史记录中

## 1.2.3 忽略文件

```
# 添加git忽略文件
$ vi .gitignore

# 忽略所有以 .o 或 .a 结尾的文件
*.[oa]

 忽略所有以波浪符（~）结尾的文件
*~
```
文件 .gitignore 的格式规范如下：
	
	• 所有空行或者以注释符号 ＃ 开头的行都会被 Git 忽略。
	• 可以使用标准的 glob 模式匹配。	
	• 匹配模式最后跟反斜杠（/）说明要忽略的是目录。	
	• 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取反
```
所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。星号（*）匹配零个或多个任意字符；[abc] 匹配
任何一个列在方括号中的字符（这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）；问号（?）
只匹配一个任意字符；如果在方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配
（比如 [0-9] 表示匹配所有 0 到 9 的数字）。
```
再看一个 .gitignore 文件的例子：

```
# 此为注释 – 将被 Git 忽略
*.a # 忽略所有 .a 结尾的文件
!lib.a # 但 lib.a 除外
/TODO # 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
build/ # 忽略 build/ 目录下的所有文件
doc/*.txt # 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
```

## 1.2.4 查看已暂存和未暂存的更新

## 1.2.5 提交更新
在每次commit之前先用 git status 文件是否都 git add 了（即暂存过了），否则不会被记录。

```
$ git commit -m " update message "
```

## 1.2.6 跳过使用暂存

```
$ git commit -a -m " update message "
或
$ git commit -am " update message "
```

## 1.2.7 移除文件
删除未暂存的文件

```
$ rm 1.txt
$ git rm 1.txt
```

强制删除
```
$ git rm 1.txt  -f 
```

## 1.2.8 移动文件

```
$ git move file_from file_to
```
相当于

```
$ mv README.txt README
$ git rm README.txt
$ git add READ
```

## 1.3 查看提交历史

```
$ git log
```
**-p** 显示提交的差异， **-2** 显示最近的两次提交
```
$ git log -p -2
```
**--stat** 仅显示简要的增改行数统计

```
$ git log --stat
```

还有个常用的 **--pretty** 选项，可以指定使用完全不同于默认格式的方式展示提交历史。比如用 **oneline** 将每个提交放在一行显示，这在提交数很大时非常有用。另外还有 **short**，**full** 和 **fuller** 可以用，展示的信息或多或少有些不同

```
# 每条记录显示一行
git log --pretty=oneline
```
自定义输出格式
```
$ git log --pretty=format:"%h - %an, %ar : %s"
a8876b9 - angus, 18 minutes ago : 添加README
f191557 - angus, 24 minutes ago : 删除README.md
```
常用格式占位符
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190308165706103.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)

# 1.4 撤消操作
## 1.4.1 修改最后一次提交

```
git commit --amend
```
如果**commit**后发现还有未 **add** 的文件，则

```
$ git commit -m "修改文件"
$ git add 1.txt
$ git commit --amend
```
上面的三条命令最终得到一个提交，第二个提交命令修正了第一个的提交内容。

## 1.4.2 取消暂存的文件
暂存的文件即已执行 **git add** 

```
$ git add *
$ git status
# On branch master
# Changes to be committed:
# (use "git reset HEAD <file>..." to unstage)
#
# modified: README.txt
```
输出告诉我们应该用 **git reset HEAD \<file> ** 

例如撤销README.txt

```
$ git reset HEAD README.txt
```

## 1.4.3 取消对文件的修改
撤销单个文件的修改
```
$ git checkout --<file>
```

撤销所有文件的修改
```
$ git checkout .
```

# 1.5 远程仓库的使用

```
$ git clone <url>
```
## 1.5.1 查看当前的远程库

```
$ git remote 
origin
```
也可以加上 -v 选项（译注：此为—verbose 的简写，取首字母），显示对应的克隆地址：

```
$ git remote --verbose
origin  https://github.com/qijianguo/git_practice.git (fetch)
origin  https://github.com/qijianguo/git_practice.git (push)
```

## 1.5.2 添加远程仓库

```
git remote add [shortname] [url]
```
要添加一个新的远程仓库，可以指定一个简单的名字，以便将来引用
```
$ git remote
origin
$ git remote add pb git://github.com/paulboone/ticgit.git
$ git remote -v
origin git://github.com/schacon/ticgit.git
pb git://github.com/paulboone/ticgit.git

```
现在可以用字串 pb 指代对应的仓库地址了。比如说，要抓取所有 Paul 有的，但本地仓库没有的信息，可以运行 git fetch pb：

```
$ git fetch pb
remote: Counting objects: 58, done.
remote: Compressing objects: 100% (41/41), done.
remote: Total 44 (delta 24), reused 1 (delta 0)
Unpacking objects: 100% (44/44), done.
From git://github.com/paulboone/ticgit
* [new branch] master -> pb/master
* [new branch] ticgit -> pb/ticgit
```
现在，Paul 的主干分支（master）已经完全可以在本地访问了，对应的名字是 pb/master，你可以将它合并到自己的某个分支，或者切换到这个分支，看看有些什么有趣的更新。

## 1.5.3 从远程抓取

```
$ git fetch [remote-nam]
```

## 1.5.4 推送数据到远程仓库

```
$ git push [remote-name] [branch-name]

```

## 1.5.5 查看远程仓库信息

```
$ git remote
origin
$ git remote show origin
```

## 1.5.6 远程仓库的删除和重命名
在新版 Git 中可以用 git remote rename 命令修改某个远程仓库的简短名称，比如想把 pb 改成 paul，可以这么运行：

```
$ git remote rename pb paul
$ git remote
origin
paul
```
注意，对远程仓库的重命名，也会使对应的分支名称发生变化，原来的 pb/master 分支现在成了 
paul/master。
	碰到远端仓库服务器迁移，或者原来的克隆镜像不再使用，又或者某个参与者不再贡献代码，那么需要移除对应的远端仓库，可以运行 git remote rm 命令

```
$ git remote rm paul
$ git remote
origin
```

