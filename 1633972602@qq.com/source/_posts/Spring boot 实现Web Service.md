---
title: Spring boot 实现Web Service
categories: Java
tags: java, springboot
---

@[TOC](Spring boot 实现Web Service)
# 为什么要用Web service？
最近公司跟其他公司有个数据对接，按照对方的传输方式来做，用的是**web service**方式，数据包格式为**xml**，业务需要双向通信，即我能发送给对方，对方也会发送给我。另外，用的是比较流行的spring boot，不了解的可以自行谷歌。
# 什么是Web service？
[百度百科](https://baike.baidu.com/item/Web%20Service/1215039)：Web service是一个平台独立的，低耦合的，自包含的、基于可编程的web的应用程序，可使用开放的XML（标准通用标记语言下的一个子集）标准来描述、发布、发现、协调和配置这些应用程序，用于开发分布式的互操作的应用程序。 Web Service技术， 能使得运行在不同机器上的不同应用无须借助附加的、专门的第三方软件或硬件， 就可相互交换数据或集成。依据Web Service规范实施的应用之间， 无论它们所使用的语言、 平台或内部协议是什么， 都可以相互交换数据。Web Service是自描述、 自包含的可用网络模块， 可以执行具体的业务功能。Web Service也很容易部署， 因为它们基于一些常规的产业标准以及已有的一些技术，诸如标准通用标记语言下的子集XML、HTTP。Web Service减少了应用接口的花费。Web Service为整个企业甚至多个组织之间的业务流程的集成提供了一个通用机制。

# 开始
省略spring boot项目搭建
## 相关依赖

```
<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web-services</artifactId>
		</dependency>
		<dependency>
			<groupId>org.apache.cxf</groupId>
			<artifactId>cxf-spring-boot-starter-jaxws</artifactId>
			<version>3.2.4</version>
		</dependency>
		<!-- https://mvnrepository.com/artifact/org.apache.axis/axis -->
		<dependency>
			<groupId>org.apache.axis</groupId>
			<artifactId>axis</artifactId>
			<version>1.4</version>
		</dependency>
		<!-- https://mvnrepository.com/artifact/javax.xml.rpc/javax.xml.rpc-api -->
		<dependency>
			<groupId>javax.xml.rpc</groupId>
			<artifactId>javax.xml.rpc-api</artifactId>
			<version>1.1.2</version>
		</dependency>

	</dependencies>
```

刚出差回来，太累了，改天补全！(着急的话首页加我微信，我给你讲解)

项目地址：https://github.com/qijianguo/webservice

