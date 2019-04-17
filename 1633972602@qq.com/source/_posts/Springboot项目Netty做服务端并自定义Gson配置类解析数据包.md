---
title: Springboot项目Netty做服务端并自定义Gson配置类解析数据包
categories: Netty
tags: netty
---

简述
> Springboot项目中使用 ***Netty*** 作为服务端，接收并处理其他平台发送的 ***Json数据包***，处理拆包、粘包及数据包中时间类型是 ***long*** 类型需转成 ***Date***的情况。

项目流程

 1. 启动项目，开启Netty服务端口11111
 2. 加载Bean
 3. 本地开启socket tool，模拟发送惊悚数据包
 4. Netty解析json包，处理特殊情况，例如 ***拆包***、***粘包***。
 5. Gson按照配置文件处理json文件，并转换成JavaBeen
 6. 入库。

项目结构（下载即可：[传送门](https://download.csdn.net/download/qq_35974759/10850042)）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181214105924997.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)
pom.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>cn.angus.demo</groupId>
    <artifactId>externalDataConnector</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <java.version>1.8</java.version>
    </properties>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.6.RELEASE</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
            <version>1.5.4.RELEASE</version>
        </dependency>
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
            <groupId>org.jboss.netty</groupId>
            <artifactId>netty</artifactId>
            <version>3.2.10.Final</version>
        </dependency>
        <dependency>
            <groupId>net.sf.json-lib</groupId>
            <artifactId>json-lib</artifactId>
            <version>2.4</version>
            <classifier>jdk15</classifier>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.16.18</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.7.0</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.7.0</version>
        </dependency>
        <dependency>
            <groupId>commons-codec</groupId>
            <artifactId>commons-codec</artifactId>
            <version>1.7</version>
        </dependency>
        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
            <version>2.8.1</version>
        </dependency>
		<dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-mongodb</artifactId>
            <version>1.10.6.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.21</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
            <version>1.5.6.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>fluent-hc</artifactId>
            <version>4.5.3</version>
        </dependency>
        <dependency>
            <groupId>io.netty</groupId>
            <artifactId>netty-all</artifactId>
            <version>4.1.30.Final</version>
        </dependency>
    </dependencies>

    <build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <mainClass>cn.angus.demo.Application</mainClass>
            </configuration>
        </plugin>
    </plugins>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <filtering>true</filtering>
        </resource>
    </resources>
</build>
</project>

```

Listener

```
package cn.angus.demo.listener;

import cn.angus.demo.consts.Ports;
import cn.angus.demo.handler.VehicleGasSyncHandler;
import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.handler.timeout.ReadTimeoutHandler;
import io.netty.util.concurrent.DefaultEventExecutorGroup;
import io.netty.util.concurrent.EventExecutorGroup;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import io.netty.handler.codec.json.JsonObjectDecoder;
import javax.annotation.PostConstruct;

@Component
@Slf4j
public class VehicleGasSyncListener {
    @Autowired
    private VehicleGasSyncHandler vehicleGasSyncHandler;

    @PostConstruct
    private void startNettyServerAsync(){
        new Thread(this::startNettyServer).start();
    }

    private void startNettyServer(){
        EventLoopGroup bossGroup = new NioEventLoopGroup();
        EventLoopGroup workerGroup = new NioEventLoopGroup();
        final EventExecutorGroup businessGroup = new DefaultEventExecutorGroup(10);
        try {
            ServerBootstrap b = new ServerBootstrap();
            b.group(bossGroup, workerGroup)
                    .channel(NioServerSocketChannel.class)
                    .option(ChannelOption.SO_BACKLOG, 1024)
                    .childOption(ChannelOption.SO_KEEPALIVE, true)
                    .childHandler(new ChannelInitializer() {
                        @Override
                        protected void initChannel(Channel channel) throws Exception {
                        	// 设置连接超时时间（很重要）
                            channel.pipeline().addLast("readtime",new ReadTimeoutHandler(60));
                            // 解决粘包问题
                            channel.pipeline().addLast(new JsonObjectDecoder());
                            channel.pipeline().addLast(businessGroup, "executer", vehicleGasSyncHandler);
                        }
                    });
            ChannelFuture f = b.bind(Ports.VEHICLE_GAS_PORT).sync();
            f.channel().closeFuture().sync();
        } catch(Exception e) {
            log.error(e.getMessage(), e);
        } finally {
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }
    }
}
```

Handler

```
package cn.angus.demo.handler;

import cn.angus.demo.dao.SpotDao;
import cn.angus.demo.domain.Request;
import cn.angus.demo.domain.Spot;
import com.google.gson.Gson;
import io.netty.buffer.ByteBuf;
import io.netty.buffer.Unpooled;
import io.netty.channel.ChannelFutureListener;
import io.netty.channel.ChannelHandler;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.ChannelInboundHandlerAdapter;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import java.nio.charset.Charset;

@Component
@ChannelHandler.Sharable
@Slf4j
public class VehicleGasSyncHandler extends ChannelInboundHandlerAdapter {

    private final Gson gson;

    @Autowired
    private SpotDao spotDao;

    private static final String SPOT = "spot";

    @Autowired
    public VehicleGasSyncHandler(Gson gson) {
        this.gson = gson;
    }

    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg){
        ByteBuf m = (ByteBuf) msg;
        String rawMsg = m.toString(Charset.forName("utf-8"));
        log.info("VehicleGasSyncHandler Receive message: " +  rawMsg);
        if (rawMsg.length() == 0 || rawMsg.length() -1 != rawMsg.lastIndexOf("}"))
            return;
        String response = persistAndResponse(rawMsg);
        if (response != null) {
            ctx.writeAndFlush(Unpooled.wrappedBuffer(response.getBytes()))
                    .addListener(ChannelFutureListener.FIRE_EXCEPTION_ON_FAILURE)
                    .addListener((ChannelFutureListener) channelFuture -> log.info("VehicleGasSyncHandler 成功发送响应{}", response));
            m.release();
        }
    }

    private String persistAndResponse(String rawMsg) {
        try {
            gson.fromJson(rawMsg, Spot.class);
        } catch (Exception e) {
            log.error(e.getMessage(), e);
        }
        // 省略返回结果。
        return "";
    }

}

```

GsonConfig.java

```
package cn.angus.demo.config;

import com.google.gson.*;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import java.util.Date;

@Configuration
public class GsonConfig {
    @Bean
    public Gson gson(){
        return new GsonBuilder()
                .setFieldNamingPolicy(FieldNamingPolicy.LOWER_CASE_WITH_UNDERSCORES)
                // Date 类型适配器
                .registerTypeAdapter(Date.class, (JsonDeserializer<Date>) (json, typeOfT, context) -> new Date(json.getAsJsonPrimitive().getAsLong()))
                .registerTypeAdapter(Date.class, (JsonSerializer<Date>) (date, type, jsonSerializationContext) -> new JsonPrimitive(date.getTime()))
                .create();
    }
}

```

实体类（createTime：将long转成Date）


```
@Data
@Document(collection = "vehicle_spot")
@JsonIgnoreProperties(ignoreUnknown = true)
public class Spot {
    @Id
    private String id;
    private String area;
    private String address;
    private String longitude;
    private String latitude;
    private Double slope;
    private Date createTime;
    private Boolean inUse;
    private Integer fuelTypeId;
    private String code;
}

```

Ports.java

```
public class Ports {
    public static final int VEHICLE_GAS_PORT = 11111;
}

```

测试用例：

```
{
    "_id" : "297e0587671b749801671b754b020000",
    "area" : "京",
    "address" : "北京",
    "longitude" : "489",
    "latitude" : "125",
    "slope" : 5.0,
    "create_time" :1544756993000,
    "fuel_type_id" : 1,
    "code" : "testCode"
}
```

mongo 入库：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181214112637648.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)
项目下载： [https://download.csdn.net/download/qq_35974759/10850042](https://download.csdn.net/download/qq_35974759/10850042)
下载后导入到Idea中，配置maven，修改application-dev中的数据库地址改为你的，启动项目，利用SocketTool模拟发送数据包。
