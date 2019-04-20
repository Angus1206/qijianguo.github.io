---
title: Netty自定义协议上传
categories: Netty
tags: netty
date: 2018-12-27 18:30:50
---
<!-- more -->
* 客户端:
```
import io.netty.bootstrap.Bootstrap;
import io.netty.channel.ChannelFuture;
import io.netty.channel.EventLoopGroup;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.nio.NioSocketChannel;
import io.netty.handler.logging.LogLevel;
import io.netty.handler.logging.LoggingHandler;

public class SocketClient {
    public static void main(String[] args) throws InterruptedException {
        EventLoopGroup eventLoopGroup = new NioEventLoopGroup();

        try {
            Bootstrap bootstrap = new Bootstrap();
            bootstrap.group(eventLoopGroup)
                    .channel(NioSocketChannel.class)
                    .handler(new LoggingHandler(LogLevel.INFO))
                    .handler(new SocketClientInitializer());

            ChannelFuture channelFuture = bootstrap.connect("localhost", 8899).sync();
            channelFuture.channel().closeFuture().sync();
        }
        finally {
            eventLoopGroup.shutdownGracefully();
        }
    }
}
```

```

import io.netty.buffer.ByteBuf;
import io.netty.buffer.UnpooledByteBufAllocator;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.ChannelInboundHandlerAdapter;

import java.io.*;

public class SocketClientHandler extends ChannelInboundHandlerAdapter {
    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
    }

    @Override
    public void channelActive(ChannelHandlerContext ctx) throws Exception {
        UnpooledByteBufAllocator allocator = new UnpooledByteBufAllocator(false);
        ByteBuf buffer = allocator.buffer(20);

//        String longMsgBody = "";

        String device = "T0001";

        String imgStr = getBytes();

        int length = imgStr.length();
        System.out.println("总长度: " + length + device.length() + 8);
        buffer.writeInt(length + device.length() + 8);

//        buffer.writeBytes(new byte[]{0x0a, 0x0d});

        buffer.writeInt(device.length());
        buffer.writeInt(imgStr.length());
        buffer.writeBytes(device.getBytes());

        buffer.writeBytes(imgStr.getBytes());

        ctx.writeAndFlush(buffer);
    }

    private static String getBytes() {
        try {
            StringBuffer sb = new StringBuffer();
            FileInputStream fis = new FileInputStream("D:/mmmm.jpg");
            BufferedInputStream bis = new BufferedInputStream(fis);
            java.io.ByteArrayOutputStream bos = new java.io.ByteArrayOutputStream();
            byte[] buff = new byte[1024];
            int len = 0;
            while ((len = fis.read(buff)) != -1) {
                bos.write(buff, 0, len);
            }
            // 得到图片的字节数组
            byte[] result = bos.toByteArray();
            System.out.println(byte2HexStr(result));
            // 字节数组转成十六进制
            String str = byte2HexStr(result);
//            /*
//             * 将十六进制串保存到txt文件中
//                    */
//            PrintWriter pw = new PrintWriter(
//                    new FileWriter("D:/mmmm.txt"));
//            pw.println(str);
//            pw.close();
            return str;
        } catch (IOException e) {
            e.printStackTrace();
        }
        return "";
    }

    /*
     * 实现字节数组向十六进制的转换方法一
     */
    public static String byte2HexStr(byte[] b) {
        String hs = "";
        String stmp = "";
        for (int n = 0; n < b.length; n++) {
            stmp = (Integer.toHexString(b[n] & 0XFF));
            if (stmp.length() == 1)
                hs = hs + "0" + stmp;
            else
                hs = hs + stmp;
        }
        return hs.toUpperCase();
    }

    private static byte uniteBytes(String src0, String src1) {
        byte b0 = Byte.decode("0x" + src0).byteValue();
        b0 = (byte) (b0 << 4);
        byte b1 = Byte.decode("0x" + src1).byteValue();
        byte ret = (byte) (b0 | b1);
        return ret;
    }

    /*
     * 实现字节数组向十六进制的转换的方法二
     */
    public static String bytesToHexString(byte[] src) {
        StringBuilder stringBuilder = new StringBuilder("");
        if (src == null || src.length <= 0) {
            return null;
        }
        for (int i = 0; i < src.length; i++) {
            int v = src[i] & 0xFF;
            String hv = Integer.toHexString(v);
            if (hv.length() < 2) {
                stringBuilder.append(0);
            }
            stringBuilder.append(hv);
        }
        return stringBuilder.toString();
    }

}
```

```
import io.netty.channel.ChannelInitializer;
import io.netty.channel.ChannelPipeline;
import io.netty.channel.socket.SocketChannel;

public class SocketClientInitializer extends ChannelInitializer<SocketChannel> {
    @Override
    protected void initChannel(SocketChannel ch) throws Exception {
        ChannelPipeline pipeline = ch.pipeline();
        pipeline.addLast(new SocketClientHandler());
    }
}
```

* 服务端 

```

import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.ChannelFuture;
import io.netty.channel.EventLoopGroup;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.handler.logging.LogLevel;
import io.netty.handler.logging.LoggingHandler;

public class SocketServer {
    public static void main(String[] args) throws InterruptedException {
        EventLoopGroup parentGroup = new NioEventLoopGroup();
        EventLoopGroup childGroup = new NioEventLoopGroup();

        try {
            ServerBootstrap serverBootstrap = new ServerBootstrap();
            serverBootstrap.group(parentGroup, childGroup)
                    .channel(NioServerSocketChannel.class)
                    .handler(new LoggingHandler(LogLevel.INFO))
                    .childHandler(new SocketServerInitializer());

            ChannelFuture channelFuture = serverBootstrap.bind(8899).sync();
            channelFuture.channel().closeFuture().sync();
        }
        finally {
            parentGroup.shutdownGracefully();
            childGroup.shutdownGracefully();
        }
    }
}

```

```

import io.netty.buffer.ByteBuf;
import io.netty.buffer.ByteBufUtil;
import io.netty.buffer.Unpooled;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.ChannelInboundHandlerAdapter;
import util.Consts;

import java.io.File;
import java.io.FileOutputStream;
import java.nio.ByteBuffer;
import java.nio.CharBuffer;
import java.nio.charset.Charset;
import java.nio.charset.CharsetDecoder;

public class BusinessServerHandler extends ChannelInboundHandlerAdapter {
    private static int count = 0;

    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        ByteBuf buf = (ByteBuf) msg;

        Consts.logger.info("BusinessServerHandler接收的二进制形式指令： " + ByteBufUtil.hexDump(buf));
        Consts.logger.info("BusinessServerHandler接收的ASCII形式指令： " + buf.toString(Charset.forName("US-ASCII")));
        System.out.println("BusinessServerHandler call count=" + ++count);

        byte[] req = new byte[buf.readableBytes()];
        buf.readBytes(req);

        int total = ByteBuffer.wrap(req, 0, 4).getInt();
        int devLength = ByteBuffer.wrap(req, 4, 4).getInt();
        int imgLength = ByteBuffer.wrap(req, 8, 4).getInt();
        String dev = getString(ByteBuffer.wrap(req, 12, devLength));
        String img = getString(ByteBuffer.wrap(req, 12 + devLength, total - 12 - devLength));

        Consts.logger.info("设备号： " + dev);
        Consts.logger.info("图片： " + img);
        saveToImgFile(img, "D:/nnnn.jpg");
    }

    /**
     * ByteBuffer 转换 String
     *
     * @param buffer
     * @return
     */
    public static String getString(ByteBuffer buffer) {
        Charset charset = null;
        CharsetDecoder decoder = null;
        CharBuffer charBuffer = null;
        try {
            charset = Charset.forName("UTF-8");
            decoder = charset.newDecoder();
            // charBuffer = decoder.decode(buffer);//用这个的话，只能输出来一次结果，第二次显示为空
            charBuffer = decoder.decode(buffer.asReadOnlyBuffer());
            return charBuffer.toString();
        } catch (Exception ex) {
            ex.printStackTrace();
            return "";
        }
    }

    public void saveToImgFile(String src, String output) {
        if (src == null || src.length() == 0) {
            return;
        }
        try {
            FileOutputStream out = new FileOutputStream(new File(output));
            byte[] bytes = src.getBytes();
            for (int i = 0; i < bytes.length; i += 2) {
                out.write(charToInt(bytes[i]) * 16 + charToInt(bytes[i + 1]));
            }
            out.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private int charToInt(byte ch) {
        int val = 0;
        if (ch >= 0x30 && ch <= 0x39) {
            val = ch - 0x30;
        } else if (ch >= 0x41 && ch <= 0x46) {
            val = ch - 0x41 + 10;
        }
        return val;
    }

}
```

```
import io.netty.buffer.ByteBuf;
import io.netty.buffer.ByteBufUtil;
import io.netty.channel.ChannelHandlerContext;
import io.netty.handler.codec.ByteToMessageDecoder;
import util.Consts;

import java.nio.ByteBuffer;
import java.nio.charset.Charset;
import java.util.List;

/**
 *     0a0d  设备号长度4   图片长度4    图片
 */
public class SelfDefineEncodeHandler extends ByteToMessageDecoder {

    private static int count = 0;

    @Override
    protected void decode(ChannelHandlerContext ctx, ByteBuf bufferIn, List<Object> out) throws Exception {
        Consts.logger.info("decode接收的二进制形式指令： " + ByteBufUtil.hexDump(bufferIn));
        Consts.logger.info("decode接收的ASCII形式指令： " + bufferIn.toString(Charset.forName("US-ASCII")));
        if (bufferIn.readableBytes() < 10) {
            return;
        }
        int beginIndex = bufferIn.readerIndex();
        int length = bufferIn.readInt();

        System.out.println("decode call count="+ ++count);
        System.out.println("bufferIn.readableBytes()="+bufferIn.readableBytes());
        System.out.println("beginIndex="+beginIndex);

        if (bufferIn.readableBytes() < length) {
            bufferIn.readerIndex(beginIndex);
            return;
        }

        bufferIn.readerIndex(beginIndex + length - 4);

        ByteBuf otherByteBufRef = bufferIn.slice(beginIndex, length);

        otherByteBufRef.retain();

        out.add(otherByteBufRef);
    }
}

```

```

import io.netty.channel.ChannelInitializer;
import io.netty.channel.ChannelPipeline;
import io.netty.channel.socket.SocketChannel;

public class SocketServerInitializer extends ChannelInitializer<SocketChannel> {
    @Override
    protected void initChannel(SocketChannel ch) throws Exception {
        ChannelPipeline pipeline = ch.pipeline();
        pipeline.addLast(new SelfDefineEncodeHandler());
        pipeline.addLast(new BusinessServerHandler());
    }
}
```

