---
title: Netty中的ChannelHandler
categories: Netty
tags: netty
time: 2018-08-03 17:12:01
---
<!-- more -->
#### FixedLengthFrameDecoder
> 数据包长度固定，解决拆包粘包
例如：一个modbus协议， 指令为如下，有 ***12个*** 字节 
```
   00 00   00 00   00 06     FF      03    00 01     00 00
  | 事务 |协议标识| 长度 | 单元标识 |功能码|起始地址|寄存器地址|
```
使用：

```
channel.pipeline().addLast(new FixedLengthFrameDecoder(frameLength));
```
#### ReadTimeoutHandler
> 读取数据超时处理, tcp长连接中常用

使用：

```
int timeoutSeconds = 10;
channel.pipeline().addLast("readtime",new ReadTimeoutHandler(timeoutSeconds));
```

#### JsonObjectDecoder
> JSON格式的数据包，解决拆包粘包问题

使用：
1. 不带构造方法的，数据包大小为 1048576 字节 = 1M。
```
channel.pipeline().addLast(new JsonObjectDecoder());
```
2. 如果JSON文件较大，比如包含多张Base64的图片，那么用无参构造就会出现收不到数据的情况，此时需要用有参构造

```
// JSON文件最大为10M
channel.pipeline().addLast(new JsonObjectDecoder(1048576 * 10));
```

pom.xml

```
	<dependency>
		<groupId>io.netty</groupId>
		<artifactId>netty-all</artifactId>
		<version>4.1.30.Final</version>
	</dependency>
```

