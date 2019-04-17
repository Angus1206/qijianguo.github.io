---
title: Windows server部署项目（前后端分离）
categories: winsw
tags: winsw
---

### 环境搭建
#### 后端：
> 安装 jdk，maven，配置环境变量
> 安装 nginx，并配置代理模式
> 安装Git

#### 前端：
> 安装node， cnpm， 配置node及cnpm环境变量

#### 安装JDK [下载传送门](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181218112016574.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)
> 1. 选择 jdk版本  2. 同意协议   3. 选择32位或64位版本。

安装：按提示进行安装，安装过程省略。最好创建一个专门的文件夹，例如我的
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181218112315361.png)
添加环境变量
我的电脑 ->  属性 -> 高级系统设置 -> 环境变量 -> 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181218112544357.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)![在这里插入图片描述](https://img-blog.csdnimg.cn/20181218112902711.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)新建JAVA_HOME
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181218112711795.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)添加JAVA_HOME到Path中

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181218112821321.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)
测试：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181218113102778.png)#### 

#### 安装Maven （略）[参考](https://blog.csdn.net/cs4380/article/details/79158268)
#### 安装MySQL （略）[参考](https://blog.csdn.net/weixin_40396510/article/details/79277731)


#### 安装Nginx 
参考的两篇文章
[Windows2012中安装Nginx并创建为Windows服务](https://www.cnblogs.com/edward2013/p/5506588.html)
[Windows下Nginx的启动、停止等命令](http://www.cnblogs.com/qianzf/p/6809427.html)
nginx.conf配置前后端分离（最底部的server）

```
events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    keepalive_timeout  65;

    server {
        listen       80;
        server_name  localhost;
		
        location / {
            root   html;
            index  index.html index.htm;
        }
        
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }	
    # 新增
    server {
        # 监听9999端口，指向root所指的文件。
        listen 9999;
        location / {
            # 前端Vue生成的dist文件地址
            root D:\workspace\WebStorm17\SUBWAY\subway2\dist;
	    index index.html index.htm;
        }
         # 前端访问的Api是http://127.0.0.1:18080/subway/...
        location /subway {
            proxy_pass http://127.0.0.1:18080/;
        }
    }
}

```
将Nginx设置为Windows服务
1.  需要借助"Windows Service Wrapper"小工具，项目地址： https://github.com/kohsuke/winsw
下载地址： http://repo.jenkins-ci.org/releases/com/sun/winsw/winsw/1.18/winsw-1.18-bin.exe
下载该工具后，将其放在 Nginx安装目录下，并重命名为nginx-service.exe，创建配置文件nginx-service.xml（名字要和工具名一样），
2. nginx-service.xml

```
<service>
  <id>nginx</id>
  <name>Nginx Service</name>
  <description>High Performance Nginx Service</description>
  <logpath>D:\xampp\nginx\logs</logpath>
  <log mode="roll-by-size">
    <sizeThreshold>10240</sizeThreshold>
    <keepFiles>8</keepFiles>
  </log>
  <executable>D:\develop\Java\nginx\nginx-1.12.2\nginx.exe</executable>
  <startarguments>-p D:\develop\Java\nginx\nginx-1.12.2</startarguments>
  <stopexecutable>D:\develop\Java\nginx\nginx-1.12.2\nginx.exe</stopexecutable>
  <stoparguments>-p D:\develop\Java\nginx\nginx-1.12.2 -s stop</stoparguments>
</service>
```
3. nginx-service.exe.config

```
<configuration>
  <startup>
    <supportedRuntime version="v2.0.50727" />
    <supportedRuntime version="v4.0" />
  </startup>
  <runtime>
    <generatePublisherEvidence enabled="false"/> 
  </runtime>
</configuration>
```
4. 在cmd中运行如下命令安装windows服务

```
D:\develop\Java\nginx\nginx-1.12.2\conf>  D:\develop\Java\nginx\nginx-1.12.2\nginx-service.exe install
```

启动nginx

```
D:\develop\Java\nginx\nginx-1.12.2\conf>  start nginx
```
重启
```
D:\develop\Java\nginx\nginx-1.12.2\conf>  nginx.exe -s reload
```
验证：打开浏览器输入

```
http://127.0.0.1
```
默认nginx端口是80，如果启动失败，请修改nginx.conf中的监听端口为其他1000-65535之间的数

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181218114705594.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)

#### 安装 Node [下载传送门](https://nodejs.org/dist/v10.14.2/node-v10.14.2-x64.msi)
按照安装步骤安装即可！
检查node和npm是否安装成功

```
C:\Users\Administrator> node -v
v7.6.0

C:\Users\Administrator>npm -v
4.1.2
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181218125512859.png)
#### 安装cnpm 
[参考博客https://blog.csdn.net/u011342720/article/details/81267908](https://blog.csdn.net/u011342720/article/details/81267908)


启动Vue项目

```
>  cnpm install
...
> npm run dev
... 
```
访问9999端口



