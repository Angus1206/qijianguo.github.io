---
title: Java工具类
categories: Java
tags: java
date: 2018-12-04 22:01:12
---

### IOUtils
* org.apache.commons.io.IOUtils

方法：
> readLines 从输入流中读取多行数据
> write 把字节. 字符等写入输出流
> toInputStream 将字符串转化为输入流
> toString 转化IO流、byte[]、Url为String
> toByteArray
> copy 拷贝文件，支持2G以上的数据
> lineIterator 从输入流返回一个迭代器，根据参数要求读取的数据量，全部读取，如果数据不够，则失败
> closeQuietly 关闭流

***pom.xml***
<!-- more -->
```
<dependency>
	 <groupId>org.apache.commons</groupId>
	<artifactId>commons-io</artifactId>
	 <version>1.3.2</version>
</dependency>
```

* 示例：
```
import org.apache.commons.io.IOUtils;
import org.apache.commons.io.LineIterator;
import java.io.*;
import java.util.List;

/**
 * @author Angus
 * @version 1.0
 * @Title: TestIOUtils
 * @Description: TODO
 * @date 2018/11/20 13:13
 */
public class TestIOUtils {

    public static void main(String[] args) {
        testIOUtils();
    }

    private static void testIOUtils() {
        File file = new File("test.json");
        FileInputStream in = null;
        FileOutputStream out = null;
        try {
            in = new FileInputStream(file);
            out = new FileOutputStream("testCopy.json");

            // 从输入流中读取多行数据
            List<String> list = IOUtils.readLines(in, "UTF-8");


            StringBuffer str = new StringBuffer();
            list.forEach(value -> {
                str.append(value);
            });
            // 把字节. 字符等写入输出流
            IOUtils.write(str, out, "UTF-8");


            // 将字符串转化为输入流
            String testWord = "Hello IoUtils!";
            InputStream in2 = IOUtils.toInputStream(testWord);

            // toString转化IO流、byte[]、Url为String
            String inStr = IOUtils.toString(in2, "UTF-8");
            byte[] bytes = IOUtils.toByteArray(in2);
            String byteStr = IOUtils.toString(bytes);

            // 拷贝文件，支持2G以上的数据
            int count = IOUtils.copy(in, out);
            String response = count > 0 ? "拷贝成功" : "拷贝失败，count > 2147483647L";

            // 从输入流返回一个迭代器，根据参数要求读取的数据量，全部读取，如果数据不够，则失败
            LineIterator lineIterator = IOUtils.lineIterator(new FileInputStream(new File("test.json")), "UTF-8");
            while (lineIterator.hasNext()) {
                String line = lineIterator.nextLine();
            }
            lineIterator.close();

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            IOUtils.closeQuietly(in);
            IOUtils.closeQuietly(out);
        }
    }
}
```
### FileUtils
* org.apache.commons.io.FileUtils

方法：
> forceMkdir 强制创建文件夹，如果父级目录不存在则先创建父级目录
> deleteDirectory 删除文件夹
> writeStringToFile 将String写入文件中，如果文件目录不存在则都创建
> copyFile 拷贝文件
> forceDelete 强制删除
> forceMkdir 强制创建目录，如果父级目录不存在则创建父级目录
> copyDirectory 拷贝文件夹！ 【警告】如下操作（创建子文件夹），会造成死循环！
> listFiles 列举目录下的文件（不是目录！）

* 示例：
```
import org.apache.commons.io.FileUtils;
import org.apache.commons.io.filefilter.DirectoryFileFilter;
import org.apache.commons.io.filefilter.FileFileFilter;
import org.apache.commons.io.filefilter.IOFileFilter;

import java.io.File;
import java.io.IOException;
import java.util.Collection;
import java.util.List;

/**
 * @author Angus
 * @version 1.0
 * @Title: TestFileUtils
 * @Description: TODO
 * @date 2018/11/20 15:44
 */
public class TestFileUtils {

    public static void main(String[] args) {
        testFileUtils();
    }

    private static void testFileUtils() {
        try {
            // 强制创建文件夹，如果父级目录不存在则先创建父级目录
            FileUtils.forceMkdir(new File("dir/testDir"));

            // 删除文件夹
            FileUtils.deleteDirectory(new File("dir"));

            // 将String写入文件中，如果文件目录不存在则都创建
            FileUtils.writeStringToFile(new File("dir/testDir/test.json"), "{\"name\": \"zs\"}");

            // 拷贝文件
            FileUtils.copyFile(new File("dir/testDir/test.json"), new File("dir/testDir/testCopy.json"));

            // 强制删除
            FileUtils.forceDelete(new File("dir/testDir/test.json"));

            // 强制创建目录，如果父级目录不存在则创建父级目录
            FileUtils.forceMkdir(new File("dir/testDir2/test"));

            // 拷贝文件夹！ 【警告】如下操作（创建子文件夹），会造成死循环！
            // FileUtils.copyDirectory(new File("dir"), new File("dir/dirCopy"));
            FileUtils.copyDirectory(new File("dir"), new File("dirCopy"));

            // 列举目录下的文件（不是目录！）
            List<String> coll = (List<String>) FileUtils.listFiles(new File("dir"), FileFileFilter.FILE, DirectoryFileFilter.DIRECTORY);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```
### StringUtils
* org.apache.commons.lang3.StringUtils

方法
> isBlank
> isEmpty
> equals
> split 字符串分割
> EMPTY 返回空字符串
> replace 替换字符串
> capitalize 首字母大写

***pom.xml***
```
<dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.8.1</version>
</dependency>
```

* 示例：
```
import org.apache.commons.lang3.StringUtils;

import java.util.Arrays;

/**
 * @author Angus
 * @version 1.0
 * @Title: TestStringUtils
 * @Description: TODO
 * @date 2018/11/20 16:20
 */
public class TestStringUtils {

    public static void main(String[] args) {
        testStringUtils();
    }

    private static void testStringUtils() {
        String str = "Hello-StringUtils!";

        String str2 = "Hello";

        boolean blank = StringUtils.isBlank(str);

        boolean empty = StringUtils.isEmpty(str);

        boolean equals = StringUtils.equals(str, str2);

        String join = StringUtils.join(Arrays.asList("hello", "StringUtils", "!"));
        System.out.println("输出结果为： [hello, StringUtils, !] ");

        // 字符串分割：默认是按空格分割
        String[] splitStrBlank = StringUtils.split(str);
        for (String s : splitStrBlank) {
            System.out.println(s);
        }
        // 自定义风格符
        String[] splitStrKey = StringUtils.split(str, "-");
        for (String s : splitStrKey) {
            System.out.println(s);
        }
        // 返回空字符串
        String emptyStr = StringUtils.EMPTY;
        System.out.println(emptyStr);

        // 替换字符串
        String replaceStr2 = StringUtils.replace(str2, "Hello", "你好！");
        System.out.println(replaceStr2);

        // 首字母大写
        String capitalizeStr2 = StringUtils.capitalize(str2.toLowerCase());
        System.out.println(capitalizeStr2);
    }
}

```
