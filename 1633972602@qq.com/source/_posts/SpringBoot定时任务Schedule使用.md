---
title: SpringBoot定时任务Schedule使用
categories: Java
tags: java, schedule
---

在开发中很多时候会用到定时任务, 以前用自定义类继承***TimerTask***

<!-- more -->

```
public class CustomTask extends TimerTask{
	@Override
	public void run() {
		// 执行业务代码
	}
}

class Main {
	public static void main(String[] args) {
		// 调用
		Timer timer= new Timer();
		Calendar calendar= Calendar.getInstance(); // 设置定时时间, 当然还有其他方式
		calendar.set(Calendar.MINUTE, 0);
		calendar.set(Calendar.SECOND, 0);
		calendar.set(Calendar.MILLISECOND, 0);
		calendar.scheduleAtFixedRate(new CustomTask(),
		timer.getTime(), 1000 * 60 * 10);
	}
}
```
[Timer和TimerTask详解:https://blog.csdn.net/xieyuooo/article/details/8607220](https://blog.csdn.net/xieyuooo/article/details/8607220)

下面进入正题, SpringBoot中优雅使用定时任务

* 添加支持
在SpringBoot的启动类①中添加注解 ***@EnableScheduling***
```
@SpringBootApplication
@EnableScheduling  // ①
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

* 使用定时任务

```
/**
 * 检查小时均值报警
 */
 @Scheduled(cron = "0 1 * * * *")
 public void overproofAlert() {
	 log.info("-----开始执行定时任务-----");
 }
```
通过上面这一个注解   ***@Scheduled(cron = "0 1 * * * *")*** 这样就可以开启定时任务了, 惊不惊喜！

Cron表达式说明 [详细说明](https://www.cnblogs.com/liuyitian/p/4108391.html)
>Cron是字符串表达式, 并由'域'和空格组成。

模版: *Seconds* *Minutes* *Hours* *DayOfMonth* *Month* *DayOfWeek* *Year*
> Year可选
> 各
> Sencods

---

这样创建的定时任务是同步的，即顺序执行。 这会遇到一个问题，当某个任务中断后会阻塞掉后面的任务， 导致其他任务‘失效’。所以配置异步是非常有必要的, 步骤如下:

* 添加Config
```
@Configuration
@EnableAsync // ① 添加注解
public class AsyncConfig {
    @Bean
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(10);  
        executor.setMaxPoolSize(200);
        executor.setQueueCapacity(10);
        executor.initialize();
        return executor;
    }
}
```

* 在定时方法上添加 ***@Async*** 注解
```
@Scheduled(cron = "0 0/1 * * * *")
@Async   // ② 
public void oneMinuteTask() {
	log.info("-----开始执行定时任务-----");
}
```
