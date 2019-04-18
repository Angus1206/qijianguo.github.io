---
title: Linux中部署Maven项目, 自定义setting.xml文件
categories: Linux
tags: linux
---

* 查看Linux中maven地址
<!-- more -->
> [root@iZbp1cj4z3w66bw25x346qZ Portal]# mvn -v
Apache Maven 3.3.1 (cab6659f9874fa96462afef40fcf6bc033d58c1c; 2015-03-14T04:10:27+08:00)
Maven home: ***/usr/local/maven***
Java version: 1.8.0_131, vendor: Oracle Corporation
Java home: /usr/java/jdk1.8.0_131/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "2.6.32-642.13.1.el6.x86_64", arch: "amd64", family: "unix"
>
> [root@iZbp1cj4z3w66bw25x346qZ Portal]# cd  /usr/local/maven
> drwxr-xr-x 2 root root  4096 Apr 26  2017 logging
-rw-r--r-- 1 root root 16404 Jul 26 16:52 settings_custom.xml
-rw-r--r-- 1 root root 16177 Apr 26  2017 settings.xml

```
/usr/local/maven/conf/settings.xml      -- 默认的setting文件

/usr/local/maven/conf/settings_custom.xml    -- 自定义的setting文件
```
* 启动项目, 在脚本中指定自己的setting.xml文件
> settings /usr/local/maven/conf/settings_custom.xml
```
mvn clean compile exec:java --settings /usr/local/maven/conf/
settings_custom.xml -Plocal-config > /dev/null 2>&1 &
```




