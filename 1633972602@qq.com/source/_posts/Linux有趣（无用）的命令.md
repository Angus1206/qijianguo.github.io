---
title: Linux有趣（无用）的命令
categories: Linux
tags: linux
date: 2018-09-04 23:33:21
---

### 准备
更新yum
```
> yum update 
```
安装wget

```
> yum install wget
```
下载EPEL
```
> wget http：//dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
```
<!-- more -->
安装EPEL

```
> rpm -ivh epel-release-6-8.noarch.rpm
```
> 知识点： 什么是EPEL？ 
> [什么是EPEL 及 Centos上安装EPEL](http://www.cnblogs.com/gaoyuechen/p/7683471.html)

### sl 命令
一列火车呼啸而过

安装sl

```
> wget install sl
```

运行sl

```
> sl
```
结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181122101424177.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)
### fortune 命令
给你一句名言警句

下载 fortune
```
> wget http://springdale.math.ias.edu/data/puias/7/x86_64/os/Addons/Packages/fortune-mod-1.99.1-17.sdl7.x86_64.rpm
```
安装 fortune

```
>  rpm -ivh fortune-mod-1.99.1-17.sdl7.x86_64.rpm

warning: fortune-mod-1.99.1-17.sdl7.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID 41a40948: NOKEY
error: Failed dependencies:
	librecode.so.0()(64bit) is needed by fortune-mod-1.99.1-17.sdl7.x86_64

```
如果出现error， 则需要安装依赖

下载librecode([网站传送门](https://altlinux.pkgs.org/sisyphus/classic-x86_64/librecode-3.6-alt11.1.1.x86_64.rpm.html))

```
> wget http://ftp.altlinux.org/pub/distributions/ALTLinux/Sisyphus/x86_64/RPMS.classic//librecode-3.6-alt11.1.1.x86_64.rpm
```
安装 librecode
```
> rpm -ivh librecode-3.6-alt11.1.1.x86_64.rpm
Preparing...                          ################################# [100%]
Updating / installing...
   1:librecode-3.6-alt11.1.1          ################################# [100%]
```
重新安装 fortune

```
>  rpm -ivh fortune-mod-1.99.1-17.sdl7.x86_64.rpm
Preparing...                          ################################# [100%]
Updating / installing...
   1:fortune-mod-1.99.1-17.sdl7       ################################# [100%]
```
效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181122104258532.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)
### cowsay 命令
一头很好玩的牛
安装

```
> yum installl cowsay -y
```
运行：

```
> cowsay "牛逼浑浑"
 ______
< 牛逼浑浑 >
 ------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```
查看其它支持的动物
```
> cowsay -l
Cow files in /usr/share/cowsay:
beavis.zen blowfish bong bud-frogs bunny cheese cower default dragon
dragon-and-cow elephant elephant-in-snake eyes flaming-sheep ghostbusters
head-in hellokitty kiss kitty koala kosh luke-koala mech-and-cow meow milk
moofasa moose mutilated ren satanic sheep skeleton small sodomized
stegosaurus stimpy supermilker surgery telebears three-eyes turkey turtle
tux udder vader vader-koala www
```

切换其它图案（如下：kiss）

```
> cowsay -f kiss  "I LOVE YOU"
 ____________
< I LOVE YOU >
 ------------
     \
      \
             ,;;;;;;;,
            ;;;;;;;;;;;,
           ;;;;;'_____;'
           ;;;(/))))|((\
           _;;((((((|))))
          / |_\\\\\\\\\\\\
     .--~(  \ ~))))))))))))
    /     \  `\-(((((((((((\\
    |    | `\   ) |\       /|)
     |    |  `. _/  \_____/ |
      |    , `\~            /
       |    \  \           /
      | `.   `\|          /
      |   ~-   `\        /
       \____~._/~ -_,   (\
        |-----|\   \    ';;
       |      | :;;;'     \
      |  /    |            |
      |       |            |

```

让cowsay说出fortune的内容

```
> fortune | cowsay -f kiss
 _____________________________________
/ NEWS FLASH!! Today the East German  \
| pole-vault champion became the West |
\ German pole-vault champion.         /
 -------------------------------------
     \
      \
             ,;;;;;;;,
            ;;;;;;;;;;;,
           ;;;;;'_____;'
           ;;;(/))))|((\
           _;;((((((|))))
          / |_\\\\\\\\\\\\
     .--~(  \ ~))))))))))))
    /     \  `\-(((((((((((\\
    |    | `\   ) |\       /|)
     |    |  `. _/  \_____/ |
      |    , `\~            /
       |    \  \           /
      | `.   `\|          /
      |   ~-   `\        /
       \____~._/~ -_,   (\
        |-----|\   \    ';;
       |      | :;;;'     \
      |  /    |            |
      |       |            |
```

### cmatrix 命令
可以做酷炫的屏保

下载cmatrix
```
> wget https://jaist.dl.sourceforge.net/project/cmatrix/cmatrix/1.2a/cmatrix-1.2a.tar.gz
```
解压并进入cmatrix
```
> tar -zxvf cmatrix-1.2a.tar.gz
> cd cmatrix-1.2a
```
安装依赖

```
> yum install ncurses-devel
```

```
>./configure && make && make install   
如果出现以下提示，则说明未安装gcc
loading cache ./config.cache
checking for a BSD compatible install... /usr/bin/install -c
checking whether build environment is sane... yes
checking whether make sets ${MAKE}... yes
checking for working aclocal... missing
checking for working autoconf... missing
checking for working automake... missing
checking for working autoheader... missing
checking for working makeinfo... missing
checking for gcc... no
checking for cc... no
configure: error: no acceptable cc found in $PATH
```
安装gcc
```
> yum -y install gcc
> yum -y install gcc-c++
>  yum install make
```

```
>./configure && make && make install   
loading cache ./config.cache
checking for a BSD compatible install... (cached) /usr/bin/install -c
checking whether build environment is sane... yes
checking whether make sets ${MAKE}... (cached) yes
checking for working aclocal... missing
checking for working autoconf... missing
checking for working automake... missing
checking for working autoheader... missing
checking for working makeinfo... missing
checking for gcc... (cached) gcc
checking whether the C compiler (gcc  ) works... yes
checking whether the C compiler (gcc  ) is a cross-compiler... no
checking whether we are using GNU C... (cached) yes
checking whether gcc accepts -g... (cached) yes
checking for a BSD compatible install... /usr/bin/install -c
checking whether make sets ${MAKE}... (cached) yes
checking for main in -lncurses... (cached) yes
checking how to run the C preprocessor... (cached) gcc -E
checking for ANSI C header files... (cached) yes
checking for fcntl.h... (cached) yes
checking for sys/ioctl.h... (cached) yes
checking for unistd.h... (cached) yes
checking for termios.h... (cached) yes
checking for termio.h... (cached) yes
checking return type of signal handlers... (cached) void
checking for putenv... (cached) yes
checking for curses.h... (cached) yes
checking for ncurses.h... (cached) yes
checking for tgetent in -lncurses... (cached) yes
Using ncurses as the termcap library
checking for use_default_colors in -lncurses... (cached) yes
checking for resizeterm in -lncurses... (cached) yes
checking for wresize in -lncurses... (cached) yes
checking for consolechars... (cached) no
checking for setfont... (cached) /usr/bin/setfont
checking for /usr/lib/kbd/consolefonts... (cached) yes
checking for /usr/share/consolefonts... (cached) no
checking for mkfontdir... (cached) /usr/bin/mkfontdir
checking for /usr/lib/X11/fonts/misc... (cached) no
checking for /usr/X11R6/lib/X11/fonts/misc... (cached) no
configure: warning:  

*** You do not appear to have an X window fonts directory in the standard
*** locations (/usr/lib/X11/fonts/misc or /usr/X11R6/lib/X11/fonts/misc). The
*** mtx.pcf font will not be installed.  This means you will probably not
*** be able to use the mtx fonts in your x terminals, and hence be unable
*** to use the -x command line switch.  Sorry about that...

creating ./config.status
creating Makefile
creating cmatrix.spec
creating config.h
config.h is unchanged
make[1]: Entering directory `/root/cmatrix-1.2a'
/bin/sh ./mkinstalldirs /usr/local/bin
  /usr/bin/install -c  cmatrix /usr/local/bin/cmatrix
make  install-man1
make[2]: Entering directory `/root/cmatrix-1.2a'
/bin/sh ./mkinstalldirs /usr/local/man/man1
 /usr/bin/install -c -m 644 ./cmatrix.1 /usr/local/man/man1/cmatrix.1
make[2]: Leaving directory `/root/cmatrix-1.2a'
 Installing matrix fonts in /usr/lib/kbd/consolefonts...
make[1]: Leaving directory `/root/cmatrix-1.2a'
```
运行

```
> cmatrix -b
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2018112211354044.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)
常用命令

```
cmatrix-a :异步滚动（默认）

cmatrix-b :随机粗体

cmatrix-B :全部粗体

cmatrix-o :使用旧风格滚动

cmatrix-x :X window 模式

cmatrix-V :显示版本信息

cmatrix-u :刷新频率，0-9，也就是滚动的快慢

cmatrix-C :显示的颜色，支持green(默认),red,blue,white,yellow,cyan,
        magenta and black

例如:使用红色

cmatrix -b -C red

使用蓝色

cmatrix -b -C blue
```
参考：http://blog.51cto.com/5232821/2146115

### figlet命令
生成艺术字
安装

```
> yum install figlet
```
运行

```
> figlet I MISS YOU
 ___   __  __ ___ ____ ____   __   _____  _   _ 
|_ _| |  \/  |_ _/ ___/ ___|  \ \ / / _ \| | | |
 | |  | |\/| || |\___ \___ \   \ V / | | | | | |
 | |  | |  | || | ___) |__) |   | || |_| | |_| |
|___| |_|  |_|___|____/____/    |_| \___/ \___/ 
                                               
```

参考：[微信公众号：Java技术栈](https://mp.weixin.qq.com/s/HXJ25E1b3J-zcVeI_Q2E7Q)
