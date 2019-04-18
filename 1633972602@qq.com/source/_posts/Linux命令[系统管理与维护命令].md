---
title: Linux命令[系统管理与维护命令]
categories: Linux
tags: linux
---

* ls: 列出工作目录所含的文件及子目录,  可以用dir代替ls
语法:   ***ls [选项] [路径或文件]***
```[例如]
-- 注释: 列出mnt目录下的文件及其子目录--
> ls /mnt
apk-update-service  git-repo-for-redmine  license_capture  lost+found  mysql  newprojects  test

-- 注释: 列出/mnt目录下文件及其子目录的详细信息--
> ls  -l  /mnt
total 40
drwxrwxrwx  2 root root  4096 Jul 22 09:56 apk-update-service
drwxr-xr-x  5 root root  4096 Jun 27  2017 git-repo-for-redmine
drwxr-xr-x  3 root root  4096 Jul 31  2017 license_capture
drwx------  2 root root 16384 Jun  6  2017 lost+found
drwxr-xr-x  3 root root  4096 Jun 10  2017 mysql
drwxr-xr-x  6 root root  4096 Oct 10  2017 newprojects
drwxr-xr-x 10 root root  4096 Nov  6  2017 test
<!-- more -->
-- 注释: 显示mnt目录下的所有文件及其目录的详细信息, 并显示文件类型标记--
> ls -alF /mnt
 ls -alF /mnt 
total 48
drwxr-xr-x   9 root root  4096 Jul 31  2017 ./
dr-xr-xr-x. 25 root root  4096 Jun  8 15:30 ../
drwxrwxrwx   2 root root  4096 Jul 22 09:56 apk-update-service/
drwxr-xr-x   5 root root  4096 Jun 27  2017 git-repo-for-redmine/
drwxr-xr-x   3 root root  4096 Jul 31  2017 license_capture/
drwx------   2 root root 16384 Jun  6  2017 lost+found/
drwxr-xr-x   3 root root  4096 Jun 10  2017 mysql/
drwxr-xr-x   6 root root  4096 Oct 10  2017 newprojects/
drwxr-xr-x  10 root root  4096 Nov  6  2017 test/
```

* pwd: 显示当前的工作目录, 即当前所在的工作目录的绝对路径名称
语法: ***pwd***
```[例如]
> pwd
/root
```

* cd: 改变当前的工作目录
语法: cd  [目录名]

```[例如]
> cd  /mnt
> pwd
/mnt
```
语法: **cd** 返回根目录中
语法: **cd - **返回上次调整的目录中

```[例如]
> pwd    -- 当前目录
/mnt       
> cd       -- 跳到根目录
/root
> cd -     -- 跳到最近一次跳转的目录中
/mnt 
```
* date: 显示或修改系统时间和日期(只有超级用户才能使用date命令修改时间, 一般用户只能用date命令显示时间)
语法: ***date [选项] '+时间格式'***

```[例如]
> date '+%y-%m-%d'         
18-10-08
```
语法: ***date '%x' 或  '%X'*** 显示日期/时间

```[显示当前时间]
> date '+today is %x %X'
today is 10/08/2018 02:21:41 PM
```
语法: ***date -s yyyyMMdd HH:mm:ss***或 ***date -s yyyyMMdd*** 或 ***date  -s HH:mm:ss*** 设置系统时间

```[例如]
> date -s "20180715 17:25"
Tue Jul 15 17:25:00 CST 2018
```
语法: ***date -d "num years/months/days ago" %y-%m-%d***当前时间的前年/月/日的时间
```[例如]
> date +%y-%m-%d                 
18-10-08
> date -d "10 days ago" +%y-%m-%d
18-09-28
```
* clear : 清除屏幕信息
语法: clear

```[例如]
> clear
```
* man:  显示命令的帮助信息
语法: ***man [命令名称]***
```[例如]
> man  clear

clear(1)                                                              clear(1)

NAME
       clear - clear the terminal screen

SYNOPSIS
       clear

DESCRIPTION
       clear clears your screen if this is possible.  It looks in the environment for the terminal type and then in the terminfo database to figure out how to clear the screen.

       clear ignores any command-line parameters that may be present.

SEE ALSO
       tput(1), terminfo(5)

       This describes ncurses version 5.7 (patch 20090207).

                                                                      clear(1)
(END) 

```

* who: 显示当前登录到系统的用户
语法: who

```[例如]
> who
root     pts/0        2018-10-08 13:26 (203.110.178.83)
-- 注释: 输出格式为
名称 [状态] 终端 时间 [活动] [进程标识]（主机名）
    * 名称：用户的登录名。
    * 状态：表明终端是否对用户都是可写的。
    * 终端：类似于pts/1、pts/2等，此终端标识在/dev目录中可找到。
    * 时间：用户登录系统的时间。
    * 活动：某个用户在自己的终端上最后一次活动发生以来到现在的时间，如果此项是个“.”，就表示一分钟内的终端活动；如果终端保持静止已经超过24小时或自从系统启动以来还没有被使用过，那么此项标记为“old”。
    * 进程标识：用户登录shell的进程id。
    * 主机名：登录到Linux系统上的客户端机器标识。
--

-- 注释: 查看运行级别-- 
> who -r  
         run-level 3  2017-07-15 15:26
> who  -w
root     + pts/0        2018-10-08 13:26 (203.110.178.83)

>  who -buT
           system boot  2017-07-15 15:26
root     + pts/0        2018-10-08 13:26   .          2887 (203.110.178.83)

```
* free: 显示内存状态(系统物理内存、虚拟内存、共享内存和系统缓存)
语法: free [选项] [-s (间隔秒数)]
```
> free
             total       used       free     shared    buffers     cached
Mem:       8061108    7875964     185144     146236     201324     558792
-/+ buffers/cache:    7115848     945260
Swap:            0          0          0
```
![free参数说明](https://img-blog.csdn.net/20181008150804369?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 * ps: 系统进程瞬间的运行状态, 非常常用, 可通过组合选项(-ef、aux)准确定位系统运行的状态
 语法: ***ps [选项]***
![ps参数](https://img-blog.csdn.net/20181008151130846?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```[例如]
> ps
  PID TTY          TIME CMD
 2887 pts/0    00:00:00 zsh
24884 pts/0    00:00:00 ps

-- 注释: (输出说明)
pid: 进程的标识符
tty: 进程所属的终端控制台
time: 进程所使用的总的cpu时间
cmd: 列出正在执行的命令行
-- 

> ps -aux 
> ps -ef 
```

* top: 类型windows中的任务管理器, 能够实时显示进程的资源占用情况, 可以按照cpu使用情况或内存占用情况进行排序
![top参数](https://img-blog.csdn.net/20181008152506209?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```[例如]
> top
top - 15:31:25 up 450 days, 4 min,  1 user,  load average: 0.00, 0.00, 0.00
Tasks: 403 total,   1 running, 402 sleeping,   0 stopped,   0 zombie
Cpu(s):  0.9%us,  0.4%sy,  0.0%ni, 98.7%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st
Mem:   8061108k total,  7873600k used,   187508k free,   201444k buffers
Swap:        0k total,        0k used,        0k free,   538576k cached

  PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND                                                                                                                                                           
 3687 git       20   0 1460m 760m  11m S  1.0  9.7 509:44.69 bundle                                                                                                                                                             
15635 gitlab-r  20   0  241m  12m  912 S  1.0  0.2 366:27.19 redis-server 

-- 注释: 
> 第一行:  
15:31:25 当前系统的时间；
up 450 days 表示系统已启动450天4分钟；
1 user当前在线的用户数为1人；
load average:0.00,0.00,0.00表示系统平均负载,3个数分别为1分钟/5分钟/15分钟前到现在的系统平均负载.

> 第二、三行
Tasks：403 total总共有403个进程；
1 running： 1个运行
402 sleeping：402个处于休眠状态
0 stopped：0个停止
0 zombie：0个僵死
Cpu(s)：0.9% us：表示用戶進程占用CPU的百分比。
0.1% sy：系统进程占用CPU的百分比。
0.0% ni：用户进程空间內改变过优先级的进程占用CPU的百分比。
99.3% id：空闲CPU占用的百分比。
0.2% wa：等待输入、输出的进程占用CPU的百分比。

> 最后两行
Mem：4059952k total：系統的物理內存大小。
4009540k used：已經使用的物理內存大小。
50412k free：目前空余內存大小。
468964k buffers：用作內核緩沖區的內存大小。
Swap：8193108k total：交換分區內存大小。
0k used：已經使用的交換分區大小。
8193108k free：空閑的交換分區大小。
2320396k cached：緩存大小。

> 进程信息区
進程信息區

進程信息區顯示了每個進程的運行狀態，我們先來看一下每列輸出的含義。

PID：进程的id。
USER：进程所有者的用户名。
pr：进程优先级。
NI：nice值。负值表示高优先级，正值表示低优先级。
VIRT：进程使用的虚拟内存总量，单位KB。VIRT=SWAP+RES。
RES：进程使用的、未被换出的物理內存大小，单位KB。RES=CODE+DATA，其中，CODE为执行代码占用的物理內存大小，DATA为数据占用的內存大小。
SHR：共享內存大小，单位KB。
S：进程状态，D表示不可中断的睡眠状态，R表示运行状态，S表示睡眠状态，T表示跟踪/停止，Z表示僵死进程。
%CPU：上次更新到现在的CPU时间占用百分比。
%MEM：进程占用的物理內存百分比。
TIME+：进程總計使用的CPU时间，单位为1/100秒。
COMMAND：正在运行进程的命令名或者命令路经。
--
```

* find: 查找文件
语法: find [路径]  

```[例如]
-- 注释: 查找大于100M的文件, 但是这样无法看文件的大小/属性等 --
> find . -type f -size +100M
./Downloads/root@cq.hbjk.com.cn
./Downloads/jdk-8u131-linux-x64.rpm

-- 注释: 查找大于100M的文件, 可以看到文件的大小/属性 --
>  find . -type f -size +100M -print0 | xargs -0 ls -l
-rw-r--r-- 1 root root 169983496 Mar 16  2017 ./Downloads/jdk-8u131-linux-x64.rpm
-rw-r--r-- 1 root root 169983496 Oct 18  2017 ./Downloads/root@cq.hbjk.com.cn

-- 注释: 查找大于100M的文件, 只看大小 --
> find . -type f -size +100M  -print0 | xargs -0 du -h
163M	./Downloads/root@cq.hbjk.com.cn
163M	./Downloads/jdk-8u131-linux-x64.rpm

-- 注释: 查找大于100M的文件, 只看大小,  并排序 --
> find . -type f -size +100M -print0 | xargs -0 du -h | sort -nr
163M	./Downloads/root@cq.hbjk.com.cn
163M	./Downloads/jdk-8u131-linux-x64.rpm

```

* du: 查看当前指定文件或目录(会递归显示子目录)占用磁盘空间大小

```[例如]
-- 注释: 查询depth为1的所有文件, 并显示文件大小--
> du -h --max-depth=1
69M	./.m2
60K	./.cache
1.4G	./Downloads
12K	./.oracle_jre_usage
8.0K	./.pki
170M	./.nvm
208M	./.npm
3.6M	./.gem
8.0K	./.pip
112K	./tmp
28M	./.bundle
56K	./.java
276K	./.ssh
4.9M	./.oh-my-zsh
296K	./.vue-templates
1.9G	.

-- 注释: 查询depth为1的所有文件, 并将文件大小单位用M, 并排序 --
> du -hm --max-depth=1 | sort -n
1	./.cache
1	./.java
1	./.oracle_jre_usage
1	./.pip
1	./.pki
1	./.ssh
1	./tmp
1	./.vue-templates
4	./.gem
5	./.oh-my-zsh
28	./.bundle
69	./.m2
170	./.nvm
208	./.npm
1400	./Downloads
1883	.

-- 注释: 查询depth为1的所有文件, 并将文件大小单位用M, 排序, 取前12个--
>du -hm --max-depth=2 | sort -nr | head -12
1883	.
1400	./Downloads
383	./Downloads/ruby-2.3.3
377	./Downloads/ruby-2.4.1
208	./.npm
170	./.nvm
167	./.nvm/versions
73	./Downloads/redis-4.0.1
69	./.m2/repository
69	./.m2
47	./Downloads/zookeeper-3.4.9
45	./Downloads/kafka_2.11-0.11.0.0


```

