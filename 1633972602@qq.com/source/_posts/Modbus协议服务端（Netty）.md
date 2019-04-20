---
title: Modbus协议服务端（Netty）
categories: Linux
tags: linux
date: 2018-08-15 23:01:55
---

<!-- more -->

## pom.xml

```

<dependency>
            <groupId>io.netty</groupId>
            <artifactId>netty-all</artifactId>
            <version>4.0.21.Final</version>
            <scope>compile</scope>
</dependency>

<dependency>
            <groupId>commons-codec</groupId>
            <artifactId>commons-codec</artifactId>
            <version>1.7</version>
<dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
</dependency>
```
## Listener

```
import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.handler.codec.FixedLengthFrameDecoder;
import io.netty.handler.timeout.ReadTimeoutHandler;
import io.netty.util.concurrent.DefaultEventExecutorGroup;
import io.netty.util.concurrent.EventExecutorGroup;
import util.Consts;

public class PortalSomCommunicationListener implements Runnable{
    private int port;

    public PortalSomCommunicationListener(int port) {
        this.port = port;
    }

    @Override
    public void run() {
        startNettyServer(port);
    }

    private void startNettyServer(int port) {
        EventLoopGroup bossGroup = new NioEventLoopGroup();
        EventLoopGroup workerGroup = new NioEventLoopGroup();
        final EventExecutorGroup businessGroup = new DefaultEventExecutorGroup(10);
        try {
            // 配置服务器的NIO线程租
            ServerBootstrap b = new ServerBootstrap();
            b.group(bossGroup, workerGroup)
                    .channel(NioServerSocketChannel.class)
                    .option(ChannelOption.SO_BACKLOG, 1024)
                    .childOption(ChannelOption.SO_KEEPALIVE, true)
                    .childHandler(new ChannelInitializer() {
                        @Override
                        protected void initChannel(Channel channel) throws Exception {
                            channel.pipeline().addLast(new FixedLengthFrameDecoder(12));
                            // 设置超时时间，防止连接过多。
                            channel.pipeline().addLast("readtime",new ReadTimeoutHandler(5));
                            channel.pipeline().addLast(businessGroup, "executer", new SomCommunicationHandler());
                        }
                    });
            // 绑定端口，同步等待成功
            ChannelFuture f = b.bind(port).sync();
            // 等待服务端监听端口关闭
            f.channel().closeFuture().sync();
        } catch(Exception e) {
        } finally {
            // 优雅退出，释放线程池资源
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }
    }

    public static void main(String[] args) {
        // 异味MODBUS协议
        new Thread(new PortalSomCommunicationListener(Consts.YIWEI_PORT)).start();
    }
}

```

## Handler

```

import domain.YiWeiSample;
import io.netty.buffer.ByteBuf;
import io.netty.buffer.ByteBufUtil;
import io.netty.buffer.Unpooled;
import io.netty.channel.ChannelFutureListener;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.ChannelInboundHandlerAdapter;
import net.Client;
import org.apache.commons.codec.binary.Hex;
import util.Consts;
import java.io.UnsupportedEncodingException;
import java.nio.ByteBuffer;
import java.nio.charset.Charset;
import java.util.ArrayList;
import java.util.List;

/**
 * 请求：
 *  00 00   00 00   00 06      FF          03      00 01       00 00
 * | 事务 |协议标识| 长度 | 单元标识 |功能码|起始地址|寄存器地址|
 *
 * 响应：
 *  00 00   00 00   00 06       FF           03     00 01      00 00
 * | 事务 |协议标识| 长度 | 单元标识 |功能码|字节个数|请求的数据|
 */
public class SomCommunicationHandler extends ChannelInboundHandlerAdapter {
    // 请求指令的长度，此协议指定12个字节长度
    private static final int commandSize = 12;
    private ByteBuf readBuf;
    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) {

        ByteBuf m = (ByteBuf) msg;
        Consts.logger.info("SomCommunicationHandler收到SOM的二进制形式指令： " + ByteBufUtil.hexDump(m));
        Consts.logger.info("SomCommunicationHandler收到SOM的ASCII形式指令： " + m.toString(Charset.forName("US-ASCII")));
        readBuf.writeBytes(m);
        m.release();
        byte[] bytes = handleData();
        if (bytes == null) return;
        ctx.channel().writeAndFlush(Unpooled.wrappedBuffer(bytes))
                .addListener(ChannelFutureListener.FIRE_EXCEPTION_ON_FAILURE)
                .addListener((ChannelFutureListener) channelFuture -> Consts.logger.info(
                        "SomCommunicationHandler "
                                + this.getClass().getName()
                                + "成功发送指令:"
                                + Hex.encodeHexString(bytes)));
    }

    private byte[] handleData() {
        while (readBuf.readableBytes() >= commandSize) {
            List<Byte> result = new ArrayList<>();
            byte[] bytes = new byte[commandSize];
            readBuf.readBytes(bytes);
            // 事务
            result.add(bytes[0]);
            result.add(bytes[1]);
            // 协议标识
            result.add(bytes[2]);
            result.add(bytes[3]);
            // 起始地址
            float startIndex = ByteBuffer.wrap(bytes, 8, 2).getShort();
            // 寄存器个数
            float numberRegister = ByteBuffer.wrap(bytes, 10, 2).getShort();

            // 发送指令
            try {
                // 数据包 AI + DI
                byte[] dataBytes = getData((int) startIndex, (int) numberRegister);
                // Length 长度:长度字段以字节计数，包括单元标识和数据字段。
                int length = 3 + (dataBytes != null ? dataBytes.length : 0);
                byte[] len = numToBytes(length);
                addBytes(result, len);
                //Unit Identifier 单元标识
                result.add((byte) 0xFF);
                // Function Code 功能码
                result.add((byte) 0x04);
                //Byte Count 字节个数
//                result.add(numToBytes(dataBytes != null ? dataBytes.length : 0));
                byte[] len2 = numToHex8((dataBytes != null ? dataBytes.length : 0));
                addBytes(result, len2);

                addBytes(result, dataBytes);

                return list2byte(result);
            } catch (UnsupportedEncodingException e) {
                Consts.logger.error(e.getMessage(), e);
            }
        }
        return null;
    }

    private byte[] list2byte(List<Byte> list) {
        if (list == null || list.size() < 0)
            return null;
        byte[] bytes = new byte[list.size()];
        int i = 0;
        for (Byte aList : list) {
            bytes[i] = aList;
            i++;
        }
        return bytes;
    }

    /**
     * @param startIndex 起始地址是指从数据的第几个字段开始, 1：系统报警， 2。。。12 数据
     * @param size 寄存器的个数 = DI + AI， DI有1个，AI有11个
     */
    private byte[] getData(int startIndex, int size) throws UnsupportedEncodingException {
        // TODO: demo数据。
        YiWeiSample sample = new YiWeiSample(
                20,
                70,
                50,
                12,
                1,
                25,
                70,
                20,
                18,
                40,
                10
        );
        //                                                  // 寄存器地址
        // DI：系统报警值，0,1,2,3,4， 1为正常
        byte [] di = numToBytes(1);                         // 1
        // AI 请求的数据data，
        List<Byte> bytes = new ArrayList<>();
        addBytes(bytes, numToBytes(sample.getAvgTemp()));   // 2
        addBytes(bytes, numToBytes(sample.getAvgHumid()));  // 3
        addBytes(bytes, numToBytes(sample.getAvgPm10()));   // 4
        addBytes(bytes, numToBytes(sample.getAvgCo2()));    // 5
        addBytes(bytes, numToBytes(sample.getInYiwei()));   // 6
        addBytes(bytes, numToBytes(sample.getInTemp()));    // 7
        addBytes(bytes, numToBytes(sample.getInPm10()));    // 8
        addBytes(bytes, numToBytes(sample.getInCo2()));     // 9
        addBytes(bytes, numToBytes(sample.getOutTemp()));   // 10
        addBytes(bytes, numToBytes(sample.getOutPm10()));   // 11
        addBytes(bytes, numToBytes(sample.getOutCo2()));    // 12

        // 处理一下startIndex, startIndex默认在区间[1,12]，1为DI，AI[2,12]
        if (startIndex == 1) {
            startIndex = 0;
        } else if (startIndex >= 2 && startIndex <= 12) {
            startIndex = (startIndex - 2) * 2;
        } else {
            Consts.logger.error("Data (as requested)获取请求数据失败， Starting Address 起始地址小于2或大于12，为：" + size);
            return null;
        }

        // 寄存器个数
        if (size < 0 || size > bytes.size()) {
            Consts.logger.error("Data (as requested)获取请求数据失败， Number of Registers 寄存器个数错误，个数为：" + size);
            return null;
        }

        bytes = bytes.subList(startIndex, (size - 1) * 2);

        List<Byte> result = new ArrayList<>();
        addBytes(result, di);
        result.addAll(bytes);
        return list2byte(result);
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause)
            throws Exception {
        Consts.logger.error("DTUHandler exception: " + cause.toString());
        ctx.close();
    }

    @Override
    public void handlerAdded(ChannelHandlerContext ctx) {
        readBuf = ctx.alloc().buffer();
    }

    @Override
    public void handlerRemoved(ChannelHandlerContext ctx) {
        readBuf.release();
        readBuf = null;
        Client.disconnect(ctx.channel());
    }

    private void addBytes(List<Byte> list, byte[] arr) {
        if (arr == null || arr.length == 0) return;
        for (byte b : arr) {
            list.add(b);
        }
    }

    private static byte[] numToHex8(int b) {
        return hexStringToBytes(String.format("%02x", b));
    }

    private static byte[] numToBytes(int b) {
        String hex = String.format("%04x", b);
        return hexStringToBytes(hex);
    }

    private static byte[] hexStringToBytes(String hexString) {
        if (hexString == null || hexString.equals("")) {
            return null;
        }
        hexString = hexString.toUpperCase();
        int length = hexString.length() / 2;
        char[] hexChars = hexString.toCharArray();
        byte[] d = new byte[length];
        for (int i = 0; i < length; i++) {
            int pos = i * 2;
            d[i] = (byte) (charToByte(hexChars[pos]) << 4 | charToByte(hexChars[pos + 1]));
        }
        return d;
    }

    private static byte charToByte(char c) {
        return (byte) "0123456789ABCDEF".indexOf(c);
    }
}
```
## Consts.java

```
import org.apache.log4j.Logger;

public class Consts {

	public static final int YIWEI_PORT = 10000;
	public static Logger logger = Logger.getRootLogger();
	
}

```
## Client.java

```

import io.netty.channel.Channel;

import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

public class Client {
    private static final Map<String, Client> devCode2Client;

    static {
        devCode2Client = new ConcurrentHashMap<>();
    }

    private static final Map<Channel, String> channel2DevCode = new ConcurrentHashMap<>();


    public static void disconnect(Channel channel) {

        if (channel2DevCode.containsKey(channel)) {
            String devCode = channel2DevCode.get(channel);
            devCode2Client.remove(devCode);
            channel2DevCode.remove(channel);
        }
    }

}
```

## YiWeiSample.java

```
public class YiWeiSample {

    private int avgTemp;
    private int avgHumid;
    private int avgPm10;
    private int avgCo2;
    private int inYiwei;
    private int inTemp;
    private int inPm10;
    private int inCo2;
    private int outTemp;
    private int outPm10;
    private int outCo2;
    private int warnData;

    public YiWeiSample(int avgTemp, int avgHumid, int avgPm10, int avgCo2, int inYiwei, int inTemp, int inPm10, int inCo2, int outTemp, int outPm10, int outCo2) {
        this.avgTemp = avgTemp;
        this.avgHumid = avgHumid;
        this.avgPm10 = avgPm10;
        this.avgCo2 = avgCo2;
        this.inYiwei = inYiwei;
        this.inTemp = inTemp;
        this.inPm10 = inPm10;
        this.inCo2 = inCo2;
        this.outTemp = outTemp;
        this.outPm10 = outPm10;
        this.outCo2 = outCo2;
    }

    public int getAvgTemp() {
        return avgTemp;
    }

    public void setAvgTemp(int avgTemp) {
        this.avgTemp = avgTemp;
    }

    public int getAvgHumid() {
        return avgHumid;
    }

    public void setAvgHumid(int avgHumid) {
        this.avgHumid = avgHumid;
    }

    public int getAvgPm10() {
        return avgPm10;
    }

    public void setAvgPm10(int avgPm10) {
        this.avgPm10 = avgPm10;
    }

    public int getAvgCo2() {
        return avgCo2;
    }

    public void setAvgCo2(int avgCo2) {
        this.avgCo2 = avgCo2;
    }

    public int getInYiwei() {
        return inYiwei;
    }

    public void setInYiwei(int inYiwei) {
        this.inYiwei = inYiwei;
    }

    public int getInTemp() {
        return inTemp;
    }

    public void setInTemp(int inTemp) {
        this.inTemp = inTemp;
    }

    public int getInPm10() {
        return inPm10;
    }

    public void setInPm10(int inPm10) {
        this.inPm10 = inPm10;
    }

    public int getInCo2() {
        return inCo2;
    }

    public void setInCo2(int inCo2) {
        this.inCo2 = inCo2;
    }

    public int getOutTemp() {
        return outTemp;
    }

    public void setOutTemp(int outTemp) {
        this.outTemp = outTemp;
    }

    public int getOutPm10() {
        return outPm10;
    }

    public void setOutPm10(int outPm10) {
        this.outPm10 = outPm10;
    }

    public int getOutCo2() {
        return outCo2;
    }

    public void setOutCo2(int outCo2) {
        this.outCo2 = outCo2;
    }

    public int getWarnData() {
        return warnData;
    }

    public void setWarnData(int warnData) {
        this.warnData = warnData;
    }
}
```

