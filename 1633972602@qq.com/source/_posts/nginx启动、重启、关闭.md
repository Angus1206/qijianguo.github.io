---
title: Nginx启动、重启、关闭
categories: Nginx
tags: nginx
date: 2018-10-31 14:23:54
---
<!-- more -->
转载自： https://blog.csdn.net/helloxiaozhe/article/details/80596138

#### 方式一：传统方法
##### 启动
```
cd usr/local/nginx/sbin
./nginx
```
##### 重启
**更改配置重启nginx**
```
kill -HUP 主进程号或进程号文件路径
或者使用
cd /usr/local/nginx/sbin
./nginx -s reload
```
**判断配置文件是否正确**

```
nginx -t -c /usr/local/nginx/conf/nginx.conf
或者
cd  /usr/local/nginx/sbin
./nginx -t
```
**关闭**

```
查询nginx主进程号

ps -ef | grep nginx

从容停止   kill -QUIT 主进程号

快速停止   kill -TERM 主进程号

强制停止   kill -9 nginx

若nginx.conf配置了pid文件路径，如果没有，则在logs目录下

kill -信号类型 '/usr/local/nginx/logs/nginx.pid'
```

#### 方式二：通过配置/etc/init.d/nginx文件来控制
这里使用的是编写shell脚本的方式来处理

vi /etc/init.d/nginx  (输入下面的代码)

```
#!/bin/bash
# nginx Startup script for the Nginx HTTP Server
# it is v.0.0.2 version.
# chkconfig: - 85 15
# description: Nginx is a high-performance web and proxy server.
#              It has a lot of features, but it's not for everyone.
# processname: nginx
# pidfile: /var/run/nginx.pid
# config: /usr/local/nginx/conf/nginx.conf
nginxd=/usr/local/nginx/sbin/nginx
nginx_config=/usr/local/nginx/conf/nginx.conf
nginx_pid=/var/run/nginx.pid
RETVAL=0
prog="nginx"
# Source function library.
. /etc/rc.d/init.d/functions
# Source networking configuration.
. /etc/sysconfig/network
# Check that networking is up.
[ ${NETWORKING} = "no" ] && exit 0
[ -x $nginxd ] || exit 0
# Start nginx daemons functions.
start() {
if [ -e $nginx_pid ];then
   echo "nginx already running...."
   exit 1
fi
   echo -n $"Starting $prog: "
   daemon $nginxd -c ${nginx_config}
   RETVAL=$?
   echo
   [ $RETVAL = 0 ] && touch /var/lock/subsys/nginx
   return $RETVAL
}
# Stop nginx daemons functions.
stop() {
        echo -n $"Stopping $prog: "
        killproc $nginxd
        RETVAL=$?
        echo
        [ $RETVAL = 0 ] && rm -f /var/lock/subsys/nginx /var/run/nginx.pid
}
# reload nginx service functions.
reload() {
    echo -n $"Reloading $prog: "
    #kill -HUP `cat ${nginx_pid}`
    killproc $nginxd -HUP
    RETVAL=$?
    echo
}
# See how we were called.
case "$1" in
start)
        start
        ;;
stop)
        stop
        ;;
reload)
        reload
        ;;
restart)
        stop
        start
        ;;
status)
        status $prog
        RETVAL=$?
        ;;
*)
        echo $"Usage: $prog {start|stop|restart|reload|status|help}"
        exit 1
esac
exit $RETVAL
```

:wq  保存并退出

***注意： nginxd和nginxconfig中写自己nginx位置！！！***

设置文件的访问权限

chmod a+x /etc/init.d/nginx   (a+x ==> all user can execute  所有用户可执行)

这样在控制台就很容易的操作nginx了：查看Nginx当前状态、启动Nginx、停止Nginx、重启Nginx…

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190307163714967.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)
同样的修改了nginx的配置文件nginx.conf，也可以使用上面的命令重新加载新的配置文件并运行，

##### 配置开机自启动：

方式一：在/etc/rc.local中配置

可以将此命令加入到rc.local文件中，这样开机的时候nginx就默认启动了

vi /etc/rc.local

加入一行  /etc/init.d/nginx start    保存并退出，下次重启会生效。


##### 将nginx配置成自启动的服务
###### 添加至服务管理列表，并让其开机自动启动

```
[root@nginx ~]# chkconfig --add nginx
[root@nginx ~]# chkconfig nginx on 
[root@nginx ~]# chkconfig nginx --list 
nginx              0:关闭    1:关闭    2:启用    3:启用    4:启用    5:启用    6:关闭
```

###### nginx启动、停止、无间断服务重启

```
[root@example ~]# service nginx start

[root@example ~]# service nginx stop

[root@example ~]# service nginx reload
```

###### 查看一下端口

```
[root@nginx ~]# netstat -ntlp | grep :80
tcp        0      0 0.0.0.0:80                  0.0.0.0:*                   LISTEN      3889/nginx

```
对于其他服务也同样适用，比如Mysql,php-fpm等等

###### 测试
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190307163951737.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)
