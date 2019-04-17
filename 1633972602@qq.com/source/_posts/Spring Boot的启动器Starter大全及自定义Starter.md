---
title: Spring Boot的启动器Starter大全及自定义Starter
categories: Java
tags: java, springboot
---

转载自：https://www.cnblogs.com/shamo89/p/8051966.html

现有启动器Starter目录
Spring Boot应用启动器基本的一共有44种，具体如下：

1）spring-boot-starter 
这是Spring Boot的核心启动器，包含了自动配置、日志和YAML。

2）spring-boot-starter-actuator 
帮助监控和管理应用。

3）spring-boot-starter-amqp 
通过spring-rabbit来支持AMQP协议（Advanced Message Queuing Protocol）。

4）spring-boot-starter-aop 
支持面向方面的编程即AOP，包括spring-aop和AspectJ。

5）spring-boot-starter-artemis 
通过Apache Artemis支持JMS的API（Java Message Service API）。

6）spring-boot-starter-batch 
支持Spring Batch，包括HSQLDB数据库。

7）spring-boot-starter-cache 
支持Spring的Cache抽象。

8）spring-boot-starter-cloud-connectors 
支持Spring Cloud Connectors，简化了在像Cloud Foundry或Heroku这样的云平台上连接服务。

9）spring-boot-starter-data-elasticsearch 
支持ElasticSearch搜索和分析引擎，包括spring-data-elasticsearch。

10）spring-boot-starter-data-gemfire 
支持GemFire分布式数据存储，包括spring-data-gemfire。

11）spring-boot-starter-data-jpa 
支持JPA（Java Persistence API），包括spring-data-jpa、spring-orm、Hibernate。

12）spring-boot-starter-data-mongodb 
支持MongoDB数据，包括spring-data-mongodb。

13）spring-boot-starter-data-rest 
通过spring-data-rest-webmvc，支持通过REST暴露Spring Data数据仓库。

14）spring-boot-starter-data-solr 
支持Apache Solr搜索平台，包括spring-data-solr。

15）spring-boot-starter-freemarker 
支持FreeMarker模板引擎。

16）spring-boot-starter-groovy-templates 
支持Groovy模板引擎。

17）spring-boot-starter-hateoas 
通过spring-hateoas支持基于HATEOAS的RESTful Web服务。

18）spring-boot-starter-hornetq 
通过HornetQ支持JMS。

19）spring-boot-starter-integration 
支持通用的spring-integration模块。

20）spring-boot-starter-jdbc 
支持JDBC数据库。

21）spring-boot-starter-jersey 
支持Jersey RESTful Web服务框架。

22）spring-boot-starter-jta-atomikos 
通过Atomikos支持JTA分布式事务处理。

23）spring-boot-starter-jta-bitronix 
通过Bitronix支持JTA分布式事务处理。

24）spring-boot-starter-mail 
支持javax.mail模块。

25）spring-boot-starter-mobile 
支持spring-mobile。

26）spring-boot-starter-mustache 
支持Mustache模板引擎。

27）spring-boot-starter-redis 
支持Redis键值存储数据库，包括spring-redis。

28）spring-boot-starter-security 
支持spring-security。

29）spring-boot-starter-social-facebook 
支持spring-social-facebook

30）spring-boot-starter-social-linkedin 
支持pring-social-linkedin

31）spring-boot-starter-social-twitter 
支持pring-social-twitter

32）spring-boot-starter-test 
支持常规的测试依赖，包括JUnit、Hamcrest、Mockito以及spring-test模块。

33）spring-boot-starter-thymeleaf 
支持Thymeleaf模板引擎，包括与Spring的集成。

34）spring-boot-starter-velocity 
支持Velocity模板引擎。

35）spring-boot-starter-web 
S支持全栈式Web开发，包括Tomcat和spring-webmvc。

36）spring-boot-starter-websocket 
支持WebSocket开发。

37）spring-boot-starter-ws 
支持Spring Web Services。

Spring Boot应用启动器面向生产环境的还有2种，具体如下：

1）spring-boot-starter-actuator 
增加了面向产品上线相关的功能，比如测量和监控。

2）spring-boot-starter-remote-shell 
增加了远程ssh shell的支持。

最后，Spring Boot应用启动器还有一些替换技术的启动器，具体如下：

1）spring-boot-starter-jetty 
引入了Jetty HTTP引擎（用于替换Tomcat）。

2）spring-boot-starter-log4j 
支持Log4J日志框架。

3）spring-boot-starter-logging 
引入了Spring Boot默认的日志框架Logback。

4）spring-boot-starter-tomcat 
引入了Spring Boot默认的HTTP引擎Tomcat。

5）spring-boot-starter-undertow 
引入了Undertow HTTP引擎（用于替换Tomcat）。

#### 自定义SpringBoot-Starter
##### 前言
我们都知道可以使用SpringBoot快速的开发基于Spring框架的项目。由于围绕SpringBoot存在很多开箱即用的Starter依赖，使得我们在开发业务代码时能够非常方便的、不需要过多关注框架的配置，而只需要关注业务即可。

例如我想要在SpringBoot项目中集成Redis，那么我只需要加入spring-data-redis-starter的依赖，并简单配置一下连接信息以及Jedis连接池配置就可以。这为我们省去了之前很多的配置操作。甚至有些功能的开启只需要在启动类或配置类上增加一个注解即可完成。

那么如果我们想要自己实现自己的Starter需要做些什么呢？下面就开始介绍如何实现自己的SpringBoot-xxx-starter。

##### 原理
首先说说原理，我们知道使用一个公用的starter的时候，只需要将相应的依赖添加的Maven的配置文件当中即可，免去了自己需要引用很多依赖类，并且SpringBoot会自动进行类的自动配置。那么 SpringBoot 是如何知道要实例化哪些类，并进行自动配置的呢？ 下面简单说一下。

第一步，SpringBoot 在启动时会去依赖的starter包中寻找 resources/META-INF/spring.factories 文件，然后根据文件中配置的Jar包去扫描项目所依赖的Jar包，这类似于 Java 的 SPI 机制。(spi介绍参阅：Java的spi介绍和简单应用)

第二步，根据 spring.factories配置加载AutoConfigure类。

最后，根据 @Conditional注解的条件，进行自动配置并将Bean注入Spring Context 上下文当中。

我们也可以使用@ImportAutoConfiguration({MyServiceAutoConfiguration.class}) 指定自动配置哪些类。

##### 实现
终于到了代码实现的步骤，接下来就开始编码我们自己的SpringBoot-starter。

第一步创建一个SpringBoot 项目，并添加下面两个依赖到pom.xml文件当中

```
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-configuration-processor</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-autoconfigure</artifactId>
    </dependency>
</dependencies>
```
其中 spring-boot-configuration-processor 的作用是编译时生成 spring-configuration-metadata.json ，此文件主要给IDE使用。如当配置此jar相关配置属性在 application.yml ，你可以用ctlr+鼠标左键点击属性名，IDE会跳转到你配置此属性的类中。

我们日常使用的Spring官方的Starter一般采取spring-boot-starter-{name} 的命名方式，如 spring-boot-starter-web 。

而非官方的Starter，官方建议 artifactId 命名应遵循{name}-spring-boot-starter 的格式。 例如：mybatis-spring-boot-starter  。

```
<groupId>com.ysc</groupId>
    <artifactId>simple-spring-boot-starter</artifactId>
    <version>1.0.0-SNAPSHOT</version>
<packaging>jar</packaging>
```
第二步编写我们的Service类
这里讲一下我们的Starter要实现的功能，很简单，提供一个Service，包含一个能够将配置文件中配置的字符串根据传入的字符进行分割的方法String[] split(String separatorChar)。

```
public class StarterService {
    private String config;

    public StarterService(String config) {
        this.config = config;
    }
    
    public String[] split(String separatorChar) {
        return StringUtils.split(this.config, separatorChar);
    }
    
}
```
第三步编写配置文件读取类

```
@ConfigurationProperties("example.service")
public class StarterServiceProperties {
    private String config;
    
    public void setConfig(String config) {
        this.config = config;
    }
    
    public String getConfig() {
        return config;
    }
 }
```
第四步，编写AutoConfigure类 ，这步是关键点

```
@Configuration
@ConditionalOnClass(StarterService.class)
@EnableConfigurationProperties(StarterServiceProperties.class)
public class StarterAutoConfigure {

    @Autowired
    private StarterServiceProperties properties;

    @Bean
    @ConditionalOnMissingBean
    @ConditionalOnProperty(prefix = "example.service", value = "enabled", havingValue = "true")
    StarterService starterService (){
        return new StarterService(properties.getConfig());
    }

}
```
解释一下代码中用到的几个注解：

@ConditionalOnClass，当classpath下发现该类的情况下进行自动配置。
@ConditionalOnMissingBean，当Spring Context中不存在该Bean时。
@ConditionalOnProperty(prefix = "example.service",value = "enabled",havingValue = "true")，当配置文件中example.service.enabled=true时。
下面列举SpringBoot中的所有@Conditional注解及作用

```
@ConditionalOnBean:当容器中有指定的Bean的条件下  
@ConditionalOnClass：当类路径下有指定的类的条件下  
@ConditionalOnExpression:基于SpEL表达式作为判断条件  
@ConditionalOnJava:基于JVM版本作为判断条件  
@ConditionalOnJndi:在JNDI存在的条件下查找指定的位置  
@ConditionalOnMissingBean:当容器中没有指定Bean的情况下  
@ConditionalOnMissingClass:当类路径下没有指定的类的条件下  
@ConditionalOnNotWebApplication:当前项目不是Web项目的条件下  
@ConditionalOnProperty:指定的属性是否有指定的值  
@ConditionalOnResource:类路径下是否有指定的资源  
@ConditionalOnSingleCandidate:当指定的Bean在容器中只有一个，或者在有多个Bean的情况下，用来指定首选的Bean @ConditionalOnWebApplication:当前项目是Web项目的条件下
```
最后一步，在resources/META-INF/下创建spring.factories文件，并添加如下内容：

```
org.springframework.boot.autoconfigure.EnableAutoConfiguration=com.example.autocinfigure.StarterAutoConfigure
```
至此，我们的一个Starter代码部分就是完成了，下面将项目安装到本地Maven仓库中。

发布
在项目根目录执行 mvn install 进行打包安装。

测试
将Starter项目的依赖添加到我们自己的SpringBoot项目中

```
<dependency>
    <groupId>com.ysc</groupId>
    <artifactId>simple-spring-boot-starter</artifactId>
    <version>1.0-SNAPSHOT</version>
 </dependency>
```
在application.yml 配置文件中添加配置信息：

```
example
  service
    enabled: true
    config： abc-des-dde,SSS-DRS-RE,SDR-SDFR-XXX
```
在本地使用JUnit进行代码测试

```
@Autowired
private StarterService starterService;

@Test
public void starterTest() {
    String[] splitArray = starterService.split(",");
    System.out.println(splitArray);
}
```
好，到这我们的一个自定义Stater就完成了
