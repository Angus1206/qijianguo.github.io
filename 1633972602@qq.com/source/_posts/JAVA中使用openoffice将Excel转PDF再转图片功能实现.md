---
title: JAVA中使用openoffice将Excel转PDF再转图片功能实现
categories: Java
tags: java,openoffice
---

* 需求

> 公司一个小项目要结尾了, 有非常多的表格, 而且非常复杂, 例如
<!-- more -->
![在这里插入图片描述](https://img-blog.csdn.net/20181018170207617?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
例如：（这表格, 有想死的心...）
![在这里插入图片描述](https://img-blog.csdn.net/20181018171047385?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

> 看到这样的表格，第一反应是n脸懵逼，这得做到啥时候啊，最后想到用一个方法，让客户自己玩！！
平台提供Excel模版下载，客户下载填写完再提交Excel，平台再将Excel转PDF，顺便将PDF转成图片，给前端展示，想法简单，方案可行，开工！

* 方案:

1. windows server下用 jacob, windows上可完美运行, 但是！！！Linux上不支持jacob！！！（非常重要！！）
2. poi + iText，貌似不错
3. openoffice，跨平台

>  本文选用openoffice，各位看官可自选用iText + poi，自己判别！当然也可以用jacob，前提是你不需要在Linux中运行项目。

* 安装软件：[下载传送门](http://www.openoffice.org/download/index.html)
4. win：

![win版本](https://img-blog.csdn.net/2018101817373025?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
下载完成后，一路next，最好默认路径安装，免得折腾
完成后 ***Win + R*** 填入 ***Dcomcnfg***  打开组件服务
![在这里插入图片描述](https://img-blog.csdn.net/2018101817414451?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
设置openoffice组件服务， 都设成SYSTEM
![在这里插入图片描述](https://img-blog.csdn.net/20181018174311265?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
再设置标识为：交互式用户
![在这里插入图片描述](https://img-blog.csdn.net/20181018174529594?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

进入到安装目录，如果路径是默认的，则在 ***C:\Program Files (x86)\OpenOffice 4\program*** 下面，
进到这个路径下，按住 ***shift***  再点击鼠标右键，***在此处打开命令窗口***，输入 ***soffice -headless-accept="socket,host=127.0.0.1,port=8100;urp;"-nofirststartwizard***， 查看是否启动成功， 黑窗口输入 ***netstat -ano*** 
![在这里插入图片描述](https://img-blog.csdn.net/20181018175507225?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
![在这里插入图片描述](https://img-blog.csdn.net/20181018175417121?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
windows下安装完毕！

参考： https://blog.csdn.net/qq_30987095/article/details/78789103

2.Linux：[下载传送门](http://www.openoffice.org/download/index.html)

![在这里插入图片描述](https://img-blog.csdn.net/20181018175750307?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
根据自己的Linux系统选择RPM包或者DEB，如果是Ubuntu则选DEB，其他就选RPM包，查看当前操作系统是ubuntu还是centos

```
[root@iZ2zeg3etf08f6snef3lgrZ ~]#  lsb_release -a
LSB Version:	:(略)
Distributor ID:	CentOS
Description:	CentOS Linux release (略)
Release:	(略)
Codename:	Core
```
下载完毕后，利用ssh工具上传到服务器上！

```
>  tar -zxvf Apache_OpenOffice_4.1.3_Linux_x86-64_install-rpm_zh-CN.tar.gz
(略)......

> ls 
zh-CN
> cd zh-CN/RPMS
> ls
openoffice-core01-4.1.3-9783.x86_64.rpm             openoffice-impress-4.1.3-9783.x86_64.rpm        openoffice-zh-CN-4.1.3-9783.x86_64.rpm
openoffice-4.1.3-9783.x86_64.rpm                openoffice-core02-4.1.3-9783.x86_64.rpm             openoffice-javafilter-4.1.3-9783.x86_64.rpm     openoffice-zh-CN-base-4.1.3-9783.x86_64.rpm
openoffice-base-4.1.3-9783.x86_64.rpm           openoffice-core03-4.1.3-9783.x86_64.rpm             openoffice-math-4.1.3-9783.x86_64.rpm           openoffice-zh-CN-calc-4.1.3-9783.x86_64.rpm
openoffice-brand-base-4.1.3-9783.x86_64.rpm     openoffice-core04-4.1.3-9783.x86_64.rpm             openoffice-ogltrans-4.1.3-9783.x86_64.rpm       openoffice-zh-CN-draw-4.1.3-9783.x86_64.rpm
openoffice-brand-calc-4.1.3-9783.x86_64.rpm     openoffice-core05-4.1.3-9783.x86_64.rpm             openoffice-onlineupdate-4.1.3-9783.x86_64.rpm   openoffice-zh-CN-help-4.1.3-9783.x86_64.rpm
openoffice-brand-draw-4.1.3-9783.x86_64.rpm     openoffice-core06-4.1.3-9783.x86_64.rpm             openoffice-ooofonts-4.1.3-9783.x86_64.rpm       openoffice-zh-CN-impress-4.1.3-9783.x86_64.rpm
openoffice-brand-impress-4.1.3-9783.x86_64.rpm  openoffice-core07-4.1.3-9783.x86_64.rpm             openoffice-ooolinguistic-4.1.3-9783.x86_64.rpm  openoffice-zh-CN-math-4.1.3-9783.x86_64.rpm
openoffice-brand-math-4.1.3-9783.x86_64.rpm     openoffice-draw-4.1.3-9783.x86_64.rpm               openoffice-pyuno-4.1.3-9783.x86_64.rpm          openoffice-zh-CN-res-4.1.3-9783.x86_64.rpm
openoffice-brand-writer-4.1.3-9783.x86_64.rpm   openoffice-gnome-integration-4.1.3-9783.x86_64.rpm  openoffice-ure-4.1.3-9783.x86_64.rpm            openoffice-zh-CN-writer-4.1.3-9783.x86_64.rpm
openoffice-brand-zh-CN-4.1.3-9783.x86_64.rpm    openoffice-graphicfilter-4.1.3-9783.x86_64.rpm      openoffice-writer-4.1.3-9783.x86_64.rpm
openoffice-calc-4.1.3-9783.x86_64.rpm           openoffice-images-4.1.3-9783.x86_64.rpm             openoffice-xsltfilter-4.1.3-9783.x86_64.rpm

[注释：运行下面命令， 生成  desktop-integration包]
>  yum localinstall *.rpm
> （略...）
> cd desktop-integration/
> ls
>  openoffice4.1.3-freedesktop-menus-4.1.3-9783.noarch.rpm  
   openoffice4.1.3-mandriva-menus-4.1.3-9783.noarch.rpm 
   openoffice4.1.3-redhat-menus-4.1.3-9783.noarch.rpm  
   openoffice4.1.3-suse-menus-4.1.3-9783.noarch.rpm

>  yum localinstall  openoffice4.1.3-redhat-menus-4.1.3-9783.noarch.rpm  

```
ok， 安装完毕了！ 启动服务
进入到默认安装目录下 
```
> cd /opt/openoffice4/program/
[注释：临时启动]
> /opt/openoffice4/program/soffice -headless -accept="socket,host=127.0.0.1,port=8100;urp;" -nofirststartwizard
[注释：或后台启动]
> /opt/openoffice4/program/soffice -headless -accept="socket,host=127.0.0.1,port=8100;urp;" -nofirststartwizard &

[注释：永久启动]
> nohup /opt/openoffice4/program/soffice -headless -accept="socket,host=127.0.0.1,port=8100;urp;" -nofirststartwizard &

[注释：查看是否启动成功]
> ps -ef|grep openoffice
root      7541  7527  0 10:19 pts/0    00:00:12 /opt/openoffice4/program/soffice.bin -headless -accept=socket,host=127.0.0.1,port=8100;urp; -nofirststartwizard
root      8964  7066  0 18:18 pts/3    00:00:00 grep --color=auto openoffice

或者

> netstat -tunlp |grep 
tcp        0      0 127.0.0.1:8100          0.0.0.0:*               LISTEN      7541/soffice.bin
```
Linux下安装成功！

开始撸码：
* pom.xml

```
		<!-- openoffice 相关依赖 -->
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>1.4</version>
        </dependency>
        <dependency>
            <groupId>org.openoffice</groupId>
            <artifactId>juh</artifactId>
            <version>3.2.1</version>
        </dependency>
        <dependency>
            <groupId>org.openoffice</groupId>
            <artifactId>ridl</artifactId>
            <version>3.2.1</version>
        </dependency>
        <dependency>
            <groupId>org.openoffice</groupId>
            <artifactId>unoil</artifactId>
            <version>3.2.1</version>
        </dependency>

	    <!-- pdf2img -->
        <dependency>
            <groupId>org.apache.pdfbox</groupId>
            <artifactId>pdfbox</artifactId>
            <version>2.0.6</version>
        </dependency>
        <dependency>
            <groupId>org.apache.pdfbox</groupId>
            <artifactId>pdfbox-tools</artifactId>
            <version>2.0.6</version>
        </dependency>
```

* OpenOfficeUtils
```

import java.awt.*;
import java.io.File;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.ConnectException;

import org.apache.commons.lang.StringUtils;

import com.artofsolving.jodconverter.DefaultDocumentFormatRegistry;
import com.artofsolving.jodconverter.DocumentConverter;
import com.artofsolving.jodconverter.DocumentFormatRegistry;
import com.artofsolving.jodconverter.openoffice.connection.OpenOfficeConnection;
import com.artofsolving.jodconverter.openoffice.connection.SocketOpenOfficeConnection;
import com.artofsolving.jodconverter.openoffice.converter.OpenOfficeDocumentConverter;
import com.artofsolving.jodconverter.openoffice.converter.StreamOpenOfficeDocumentConverter;
import org.apache.pdfbox.pdmodel.PDDocument;
import org.apache.pdfbox.pdmodel.PDPage;
import org.apache.pdfbox.pdmodel.PDPageTree;
import org.apache.pdfbox.rendering.ImageType;
import org.apache.pdfbox.rendering.PDFRenderer;
import org.apache.pdfbox.tools.imageio.ImageIOUtil;

import java.awt.image.BufferedImage;
import java.awt.image.RenderedImage;
import java.io.File;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;
import javax.imageio.ImageIO;

/**
 * 
 * @author josnow
 * @date 2017年5月9日 下午12:38:39
 * @version 1.0.0
 * @desc openoffice转换工具
 */
public class OpenOfficeUtils {

    public static final String LOCAL_HOST = "localhost";
    public static final int LOCAL_PORT = 8100;

    // Format
    public static DocumentFormatRegistry formatFactory = new DefaultDocumentFormatRegistry();

    /**
     *
     * @desc
     * @auth josnow
     * @date 2017年6月9日 下午4:11:04
     * @param inputFilePath
     *            待转换的文件路径
     * @param outputFilePath
     *            输出文件路径
     */
    public static void convert(String inputFilePath, String outputFilePath) throws ConnectException {
        convert(inputFilePath, outputFilePath, LOCAL_HOST, LOCAL_PORT);
    }

    /**
     *
     * @desc
     * @auth josnow
     * @date 2017年6月9日 下午4:12:29
     * @param inputFilePath
     *            待转换的文件路径
     * @param outputFilePath
     *            输出文件路径
     * @param connectIp
     *            远程调用ip
     * @param connectPort
     *            远程调用端口
     */
    public static void convert(String inputFilePath, String outputFilePath, String connectIp, int connectPort)
            throws ConnectException {
        if (StringUtils.isEmpty(inputFilePath) || StringUtils.isEmpty(outputFilePath)
                || StringUtils.isEmpty(connectIp)) {
            throw new IllegalArgumentException("参数异常！！");
        }
        OpenOfficeConnection connection = new SocketOpenOfficeConnection(connectIp, connectPort);
        connection.connect();
 
// 		  TODO Excel转成PDF默认是A4纸
//        DocumentConverter converter = getConverter(connectIp, connection);
//        converter.convert(new File(inputFilePath), new File(outputFilePath));

// 		  TODO Excel转成PDF默认是A4纸， 如果现实折行，则自定义ConverterDocument，改变纸张大小
        ConverterDocument converterDocument = new ConverterDocument(connection);
        converterDocument.convert(new File(inputFilePath), new File(outputFilePath));

        connection.disconnect();
    }

    /**
     *
     * @desc
     * @auth josnow
     * @date 2017年6月9日 下午4:08:26
     * @param inputStream
     * @param inputFileExtension
     *            待转换文件的扩展名，例如: xls，doc
     * @param outputStream
     * @param outputFileExtension
     *            输出文件扩展名，例如：pdf
     */
    public static void convert(InputStream inputStream, String inputFileExtension, OutputStream outputStream,
            String outputFileExtension) throws ConnectException {
        convert(inputStream, inputFileExtension, outputStream, outputFileExtension, LOCAL_HOST, LOCAL_PORT);
    }

    /**
     *
     * @desc
     * @auth josnow
     * @date 2017年6月9日 下午4:10:21
     * @param inputStream
     * @param inputFileExtension
     *            待转换文件的扩展名，例如: xls，doc
     * @param outputStream
     * @param outputFileExtension
     *            输出文件扩展名，例如：pdf
     * @param connectIp
     *            远程调用ip
     * @param connectPort
     *            远程调用端口
     */
    public static void convert(InputStream inputStream, String inputFileExtension, OutputStream outputStream,
            String outputFileExtension, String connectIp, int connectPort) throws ConnectException {

        if (inputStream == null || StringUtils.isEmpty(inputFileExtension) || outputStream == null
                || StringUtils.isEmpty(outputFileExtension) || StringUtils.isEmpty(connectIp)) {
            throw new IllegalArgumentException("参数异常！！");
        }

        OpenOfficeConnection connection = new SocketOpenOfficeConnection(connectIp, connectPort);
        connection.connect();
        DocumentConverter converter = getConverter(connectIp, connection);

        converter.convert(inputStream, formatFactory.getFormatByFileExtension(inputFileExtension), outputStream,
                formatFactory.getFormatByFileExtension(outputFileExtension));
        connection.disconnect();
    }

    private static DocumentConverter getConverter(String connectIp, OpenOfficeConnection connection) {
        DocumentConverter converter = "localhost".equals(connectIp) || "127.0.0.1".equals(connectIp)
                || "0:0:0:0:0:0:0:1".equals(connectIp) ? new OpenOfficeDocumentConverter(connection)
                        : new StreamOpenOfficeDocumentConverter(connection);
        return converter;
    }

    public static void main(String[] args) throws Exception {
        OpenOfficeUtils.convert("D:\\test.xls", "D:\\test.pdf");
        pdf2image(new File("D:\\test.pdf"));
    }

    /**
     * pdf to big image
     * github: https://github.com/mmtou/pdf2img
     * @param pdfFile pdf文件路径
     * @return big image file
     * @throws Exception
     */
    public static File pdf2image(File pdfFile) throws Exception {
        // 拼成图片后的宽度和高度
        int w = 0;
        int h = 0;
        List<BufferedImage> images = new ArrayList();
        // 生成图片后的路径
        String path = pdfFile.getParent() + File.separator;
        String fileName = pdfFile.getName().replace(".pdf", "");

        File destinationFile = new File(path);
        if (!destinationFile.exists()) {
            destinationFile.mkdir();
        }
        PDDocument document = PDDocument.load(pdfFile);
        PDPageTree list = document.getDocumentCatalog().getPages();
        int pageCounter = 0;
        for (PDPage page : list) {
            PDFRenderer pdfRenderer = new PDFRenderer(document);
            BufferedImage image = pdfRenderer.renderImageWithDPI(pageCounter, 100, ImageType.RGB);
            String target = path + fileName + "-" + (pageCounter++) + ".png";
            ImageIOUtil.writeImage(image, target, 100);

            w = image.getWidth();
            h += image.getHeight();
            images.add(image);
            new File(target).delete();
        }
        document.close();

        BufferedImage combined = new BufferedImage(w, h, BufferedImage.TYPE_INT_ARGB);
        Graphics g = combined.getGraphics();

        int y = 0;
        for (BufferedImage image : images) {
            g.drawImage(image, 0, y, null);
            y += image.getHeight();
        }

        // Save as new image
        File image = new File(path, fileName + ".png");
        ImageIO.write(combined, "PNG", image);
        return image;
    }

}
```
* 自定义 ConverterDocument

```

import com.artofsolving.jodconverter.openoffice.connection.OpenOfficeConnection;
import com.artofsolving.jodconverter.openoffice.converter.OpenOfficeDocumentConverter;
import com.artofsolving.jodconverter.openoffice.converter.StreamOpenOfficeDocumentConverter;
import com.sun.star.awt.Size;
import com.sun.star.beans.PropertyValue;
import com.sun.star.lang.XComponent;
import com.sun.star.uno.UnoRuntime;
import com.sun.star.view.PaperFormat;
import com.sun.star.view.XPrintable;

public class ConverterDocument extends StreamOpenOfficeDocumentConverter {


    public ConverterDocument(OpenOfficeConnection connection) {
        super(connection);
    }


    public final static Size A5, A4, A3;
    public final static Size B4, B5, B6;
    public final static Size KaoqinReport;

    static {
        A5 = new Size(14800, 21000);
        A4 = new Size(21000, 29700);
        A3 = new Size(29700, 42000);

        B4 = new Size(25000, 35300);
        B5 = new Size(17600, 25000);
        B6 = new Size(12500, 17600);

//        KaoqinReport = new Size(29700, 27940);  //最大限度  宽 1600000
        KaoqinReport = new Size(25000, 21000);;
    }


    @Override
    protected void refreshDocument(XComponent document) {
        super.refreshDocument(document);

        // The default paper format and orientation is A4 and portrait. To
        // change paper orientation
        // re set page size
        XPrintable xPrintable = (XPrintable) UnoRuntime.queryInterface(XPrintable.class, document);
        PropertyValue[] printerDesc = new PropertyValue[2];

        // Paper Orientation
//  printerDesc[0] = new PropertyValue();
//  printerDesc[0].Name = "PaperOrientation";
//  printerDesc[0].Value = PaperOrientation.PORTRAIT;

        // Paper Format
        printerDesc[0] = new PropertyValue();
        printerDesc[0].Name = "PaperFormat";
        printerDesc[0].Value = PaperFormat.USER;

        // Paper Size
        printerDesc[1] = new PropertyValue();
        printerDesc[1].Name = "PaperSize";
        printerDesc[1].Value = KaoqinReport;

        try {
            xPrintable.setPrinter(printerDesc);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
* 使用：

```
	public static void main(String[] args) throws Exception {
        OpenOfficeUtils.convert("D:\\test.xls", "D:\\test.pdf");
        pdf2image(new File("D:\\test.pdf"));
	}
```


参考： https://blog.csdn.net/liuhualiang/article/details/14094019?utm_source=blogxgwz6

