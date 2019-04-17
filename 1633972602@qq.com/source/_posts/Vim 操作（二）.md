---
title: Vim 操作（二）
categories: Vim
tags: vim
---

#### 操作多文件
> vi 1.txt  2.txt  3.txt 同时打开或创建多个文件
> :next 切换到下一个文件
> :previous 切换到上一个文件
> :last 切换到最后一个文件
> :first 切换到第一个文件
> :wnext 保存并切换到下一个文件
> :wprevious 保存并切换到上一个文件
> :hide e file 切换到file文件，当完成操作wq后按entry会切换回来

#### 保存
> :w file 将修改后的文件另存为file2，源文件内容不更改
> :20,$w file 将文件20行到末尾的另存为file
> :.,20w file 将文件20行之前的内容另存为file
> :1,2w file 将1,2行的内容另存为file

#### 拷贝
> :r file 将file文件内容拷贝到当前光标的下一行
> :3r file 将file文件内容拷贝到当前文件第三行的下一行
> :$r file 将file文件内容拷贝到当前文件的行尾
> :0r file 将file文件内容拷贝到当前文件的行首
> :/parttern/r filename ：还可以使用正则表达式，插入到匹配出的后面一行，需要注意的是如果有多处匹配，它只插入到首个匹配


#### 模版 ab
> ab  attr  phrase 
>> 例如： **:ab psvm public static void main(String[] args) {}** 
>
> ab 列出所有的模版

#### 模版map

