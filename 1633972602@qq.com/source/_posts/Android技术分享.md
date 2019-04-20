---
title: Android技术分享
categories: Android
tags: android
date: 2019-02-26 21:34:14
---

## Android入门实践
#### 什么是Android
> Android是基于Linux内核开发、主要以Java为开发语言的一个开源的操作系统，主要用于移动设备，如智能手机和平板电脑、可穿戴设备等，当然还有更多的领域使用到了Android系统，例如电视、机顶盒等
[为什么选用Linux内核](https://www.cnblogs.com/hejing-swust/articles/7821968.html)

<!-- more -->

#### Android的历史
（这儿把红框内容做成一个图片，例如乔布斯图、Linux图、Java图）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190108173528186.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)** [Steve Jobs – “No One Is Going To Buy A Big Phone”](https://www.talkandroid.com/6793-steve-jobs-no-one-is-going-to-buy-a-big-phone/)
** [Linux团队从Linux内核主线中除名](http://www.cnblogs.com/hoys/archive/2012/02/17/2356446.html)
** Oracle起诉Google，称未取得授权。

> 你可能看到今日辉煌的Android，移动操作系统市场90%的占有率，他的历史可谓是相当曲折、一言难尽的
> 关键字： 2003年、Andy Rubin、Linux内核、Java、 google、2007开源、2008发布第一个版本

#### Android的架构
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019010817373248.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)
#### Android版本
http://developer.android.com/abort/dashboards/
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190108174634497.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)[统计](https://developer.android.com/about/dashboards/?hl=zh-cn)
#### Android系统提供了哪些东西给developer？
###### 四大组件
* Activity 活动
> 
* Service服务
> 塞班挂QQ，IOS和Windows Phone期初是不支持后台运行的，后来慢慢的认识到其重要性所以后台慢慢加入了，而安卓从一开始就支持后台功能，既使应用程序在关闭的情况下也会在后台运行。
* Broadcast Receiver广播接收器
> 
* 内容提供器
> 用于在不同的程序之间共享数据，即将一些数据共享给他应用，这些数据是一些可以进行二次开发的基础数据，例如：电话簿、短信、多媒体等的数据，他们都实现了跨程序数据共享的功能
##### 丰富的系统组件
> Android为开发者提供了丰富的系统组件，可以帮助开发者设计出漂亮的界面，当然，随着也可以定制属于自己的控件。
#### SQLite 数据库
> Andrid 自带的数据库，支持SQL语法，也有封装好的API，存储和读取非常方便。（在这加代码，记住用户名和密码）

#### 强大的多媒体
> 音乐、视频、录音、拍照等，可以用代码进行控制。
> 连接真机。
> 通知

#### 地理位置定位 
> 移动设备相对于PC，地理定位功能应该可以算是一个很大的亮点，结合强大的地图功能，[LBS](https://baike.baidu.com/item/LBS/1742?fr=aladdin) 这一领域潜力非常大。例如外卖、打车、团购、社交、侦查等。

#### 环境搭建 
1.  挑选一个顺手的开发工具。开发工具有Eclipse和Android Studio，Android项目早期是用Eclipse开发的，需要安装很多插件，开发Android需要安装ADT。而Android Studio是基于Intellij IDEA开发的，插件安装非常简单，还集成了很多工具。比较推荐[Android Studio](https://developer.android.google.cn/studio/)
2. [JDK](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)：Java开发工具包
3. [Android SDK](https://blog.csdn.net/zzy1078689276/article/details/80380118#sdk%E5%8F%8A%E5%85%B6%E7%9B%B8%E5%85%B3%E8%B5%84%E6%BA%90%E7%9A%84%E4%B8%8B%E8%BD%BD%E5%9C%B0%E5%9D%80)：谷歌提供的Android开发工具包。在开发时我们需要引入该工具包来使用相关API

## Hello World
```
#include <stdio.h>
int main()
{
	printf("Hello,World!\n");
	return 0;
}
```
开始Hello World之路

* 分析Hello World案例
* 项目结构分析
* Android Studio使用

## 详细介绍
#### UI开发
1. 如何编写程序界面
2. 常用控件的使用
2.1 TextView
2.2 Button
2.3 EditView
2.4 ImageView
2.5 ListView
2.6 ...
3. 4种基本布局
3.1 线性
3.2 相对
3.3 帧布局
3.4 百分比布局

####0【‘
 Activity
1. TextView 、Button、EditText、Spinner、LinearLayout、自定义View等
2. 活动的生命周期
3. 活动间的信息传递

#### Boardcast
1. 静态活动
2. 动态活动
3. 自定义活动

#### Content Provider


#### Service

#### 网络
* 子线程
> 将耗时的操作放到子线程中去，这里就需要用到java的多线程操作
* 更新UI
> 不允许在子线程中更新UI，因为UI是线程不安全的，必须在主线程中更新，否则会异常。
* 异步消息处理机制
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190109151332630.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)
* AsyncTask

#### 


#### 权限
http://developer.android.com/reference/android/Manifest.permission.html


## 应用上线
Android系统要求所有的应用软件必须有一个签名文件，直接通过AS运行程序会使用一个默认的签名文件(仅仅适用于开发阶段)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019011814113460.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)AS生成签名
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190118141413206.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)Gradle生成签名
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190118141739246.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)



## 分享


## 总结
### 提问
* [安卓为什么会越来越卡](https://www.cnblogs.com/zhuwenlubin/p/5483046.html)
超出了硬件的范畴，

一方面：
IOS 是基于Unix的，而Unix不是开源的操作系统，iOS同样也没有开源。 iPhone之所以不会出现“越用越卡”的情况，是因为苹果公司对它的手机从硬件到软件拥有最高的管理权限，在封闭式的环境中，来自第三方的应用程序无法调用超过iPhone承受限度的指令，自然也不可能造成持续性的系统损伤。

而安卓是基于LInux，由于Linux核心设定应用在调取系统功能时一定要取得ROOT权限，这也导致大量应用因为单一功能的实现需求而获得整个ROOT层面的支配，可以在Android手机的任意储存位置进行读写，这种高自由度无异于开启了潘多拉魔盒，让Android手机无法对恶意App事先设防

另一方面：
一个是宏内核，一个是微内核，宏内核会把每个应用程序当成是系统自身的一部分，而微内核会将CPU分为几部分，当分配的CPU足够支撑应用时其他空闲CPU不会参与。房子的模型。。


如何解决：
1. android在4.4后将应用程序的运行模式由Dalvik换成了ART，简单理解就是预编译。在安装的时候就编译成本地的机器码，减少了启动和运行中的时间
2. 定制系统，例如小米的MIUI、华为的。。都是在Linux内核上打补丁。




