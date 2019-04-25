---
title: Java多线程技能
categories: Java多线程编程核心技术
tags: 多线程
date: 2019-04-20 20:21:19
---
# 进程和多线程的概念及线程的优点
## 进程
操作系统的基础；是一次程序的执行；是系统进行资源分配和调度的一个独立单位。

## 线程
进程中独立运行的子任务。

## 多线程的优点
可以极大的利用CPU的空闲时间来处理其他的任务，即同一时间内运行更多不同种类的任务。

# 使用多线程
一个进程中至少有一个线程在运行

```
public class MainThread {
    public static void main(String[] args) {
        System.out.println(Thread.currentThread().getName());
    }
}
```

## 实现多线程的两种方式

1⃣️继承Thread           2⃣️实现Runnable

### 继承Thread类
类只能单根继承，不可多继承
```
public class MyThread extends Thread {

    @Override
    public void run() {
        super.run();
        System.out.println("MyThread");
    }

    public static void main(String[] args) {
        MyThread myThread = new MyThread();
        myThread.start();
        System.out.println("运行结束！");
    }
}
```
运行结果：
```
运行结束！
MyThread
```
结论：在执行多线程时，代码的运行结果与代码执行顺序或调用顺序是无关的。（线程的随机性）

### 实现Runnable接口
接口可以多实现
Thread实现了Runnable接口
```
public class MyRunnable implements Runnable {
    public void run() {
        System.out.println("MyRunnable");
    }

    public static void main(String[] args) {
        new Thread(new MyRunnable()).start();
        System.out.println("执行了");
    }
}
```
运行结果：
```
MyRunnable
执行了
```
结论：推荐使用Runnable接口，因为Java类只能单继承

### 实例变量和线程安全
自定义线程中的实例变量针对其他线程有共享和不共享之分。
#### 不共享

```
public class NoSharedThread extends Thread{

    private int count = 5;

    public NoSharedThread(String name) {
        this.setName(name);
    }

    @Override
    public void run() {
        super.run();

        while (count > 0) {
            System.out.println(Thread.currentThread().getName() + ": " + count);
            count --;
        }

    }

    public static void main(String[] args) {
        new NoSharedThread("线程1").start();
        new NoSharedThread("线程2").start();
        new NoSharedThread("线程3").start();
    }
}
```
运行结果
```
线程1: 5
线程2: 5
线程2: 4
线程2: 3
线程2: 2
线程2: 1
线程1: 4
线程1: 3
线程1: 2
线程1: 1
线程3: 5
线程3: 4
线程3: 3
线程3: 2
线程3: 1
```
结论：每个线程使用各自的count变量

#### 共享

分析 ***i - -*** ：

```
1. 取得原有i的值
2. 计算 i - 1
3. 将 i - 1 的值赋给 i

结论：如果有多个线程同时访问i，那么会垂岸非线程安全问题
```


未加锁
```
public class SharedThread extends Thread{

    private int count = 5;

    @Override
    public void run() {
        super.run();
        count--;		// 1⃣️ 线程安全出现在这儿！！！
        System.out.println(Thread.currentThread().getName() + ": " + count);

    }

    public static void main(String[] args) {
        SharedThread sharedThread = new SharedThread();
        Thread t1 = new Thread(sharedThread, "a");
        Thread t2 = new Thread(sharedThread, "b");
        Thread t3 = new Thread(sharedThread, "c");
        Thread t4 = new Thread(sharedThread, "d");
        Thread t5 = new Thread(sharedThread, "e");

        t1.start();
        t2.start();
        t3.start();
        t4.start();
        t5.start();
    }
}

```
运行结果：

```
b: 3
a: 3
c: 2
d: 1
e: 0
```
结论： 出现了a、b两个读到相同的值3，造成线程不安全。


解决方式：

给 run方法加锁

```
   @Override
   synchronized  public void run() {
        super.run();
        count--;		// 1⃣️ 线程安全出现在这儿！！！
        System.out.println(Thread.currentThread().getName() + ": " + count);
    }
```
分析：多个线程访问run方法时，以排队的方式进行处理，当一个线程调用run方法前，先检查run方法是否被上锁，如果是则等待其他线程执行完释放锁后才能执行。如果否则对run方法加锁，执行结束后释放锁。加锁的这块代码称为**互斥区**或**临界区**。


### 留意i- -与System.out.println()的异常
结论：println()方法在内部是同步的，但i- -的操作确实在进入println()之前发生的，所以有发生非线程安全问题的概率。
所以为了防止发生非线程安全问题，还是应继续使用同步方法。


## currentThread() 方法
currentThread()可返回代码段被哪个线程调用的信息

例如：
```

public class CurrentThread extends Thread {

    public CurrentThread() {
        System.out.println("构造方法执行：" + Thread.currentThread().getName());
    }

    @Override
    public void run() {
        super.run();
        System.out.println("run方法执行：" + Thread.currentThread().getName());
    }


    public static void main(String[] args) {
        CurrentThread currentThread = new CurrentThread();
        // 自动调用run方法
        currentThread.start();
        // 手动调用run方法
        currentThread.run();
    }
}
```
运行结果：

```
构造方法执行：main
run方法执行：Thread-0
run方法执行：main
```
结论：
1.构造方法被main线程调用，run方法被Thread-0线程调用，run方法是自动调用的方法，
如果手动调用run方法，则是被main线程调用的
2.构造方法优先执行与run方法

## isAlive()方法
isAlive()是检查线程是否处于活动状态
活动状态指的是线程已经启动且尚未终止。线程处于正在运行或准备开始运行的状态，就认为是“存活的”。

```
public class IsAliveThread extends Thread {

    @Override
    public void run() {
        super.run();
        System.out.println("run:" + Thread.currentThread().isAlive());
    }

    public static void main(String[] args) {
        IsAliveThread isAliveThread = new IsAliveThread();
        System.out.println("begin: " + isAliveThread.isAlive());
        isAliveThread.start();
        System.out.println("end: " + isAliveThread.isAlive());
    }
}
```
运行结果：

```
begin: false
end: true
run:true
```
或

```
begin: false
end: true
run:false
```
结论：start方法后线程状态可能存活可能不存活

## sleep()方法
在指定的毫秒数内让当前执行的线程休眠（暂停执行），当前执行的线程是指this.currentThread()返回的线程。

```
public class SleepThread extends Thread {

    @Override
    public void run() {
        super.run();
        try {
            System.out.println("run :" + this.currentThread().getName() + " begin");
            sleep(10);
            System.out.println("run :" + this.currentThread().getName() + " end");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        SleepThread sleepThread = new SleepThread();
        System.out.println("begin: " + System.currentTimeMillis());
        sleepThread.start();
        System.out.println("end: " + System.currentTimeMillis());
    }
}
```
运行结果：

```
begin: 1555750009067
end: 1555750009074
run :Thread-0 begin
run :Thread-0 end
```
分析： 由于main和Thread-0是异步的，所以先打印begin和end，而SleepThread是随后运行的，所以打印出run begin和run end

## getId() 方法
获取线程的唯一标识

```
public static void main(String[] args) {
        System.out.println(Thread.currentThread().getName() + " : "+ currentThread().getId());
    }
```
运行结果：

```
main : 1
```
分析：当前线程名为main，id为1。

## 停止线程
在线程执行完成之前停止正在做的操作。禁止用Thread.stop()方法，因为是不安全（unsafe）的，并且已经被废弃（deprecated）了。
大多数停止线程使用Thread.interrupt()方法，但是这个方法不会终止一个正在运行的线程，还需要加入一个判断才可以完成线程的停止。
Java中有3个终止线程的方法：
1.使用退出标志，使线程正常退出，也就是当run方法完成后线程终止
2.使用stop强行终止，不推荐
3.使用interrupt方法中断线程

### 停止不了的线程
interrupt方法并不像for-break一样马上停止执行，而是仅仅在当前线程中打了一个停止的标记，并不是真正的停止线程。

```
public class InterruptThread extends Thread {

    @Override
    public void run() {
        super.run();
        for (int i = 0; i < 1000000; i ++ ) {
            System.out.println("i = " + (i + 1));
        }
    }

    public static void main(String[] args) throws InterruptedException {
        InterruptThread interruptThread = new InterruptThread();
        interruptThread.start();
        sleep(5000);
        interruptThread.interrupt();
    }
}
```
运行结果：

```
省略...
i = 999990
i = 999991
i = 999992
i = 999993
i = 999994
i = 999995
i = 999996
i = 999997
i = 999998
i = 999999
i = 1000000
```
分析：调用interrupt方法并没有让线程停下来！！

### 判断线程是否是停止状态
Thread.java类中提供了两种方法
1.boolean   this.interrupted()：测试当前线程是否已经中断
2.boolean   this.isInterrupted()：测试线程是否已经中断

```
public class InterruptThread extends Thread {

    @Override
    public void run() {
        super.run();
        for (int i = 0; i < 1000; i ++ ) {
            System.out.println("i = " + (i + 1));
        }
    }

    public static void main(String[] args) throws InterruptedException {
        InterruptThread interruptThread = new InterruptThread();
        interruptThread.start();
        sleep(1000);
        interruptThread.interrupt();
        System.out.println("是否停止1？"+ interruptThread.interrupted());
        System.out.println("是否停止2？"+ interruptThread.interrupted());
    }
}

```

运行结果：

```
省略...
i = 994
i = 995
i = 996
i = 997
i = 998
i = 999
i = 1000
是否停止1？false
是否停止2？false
```
分析：判断interruptThread对象是否停止，但控制台打印的结果显示，并未停止。验证了interrupted()方法的定义：测试当前线程是否已经中断，而当前线程是 main，从未中断过。所以打印的是两个false。

如何在当前线程中产生中断效果呢？

```
public class IsInterruptThread {
    public static void main(String[] args) {
        System.out.println("begin");
        Thread.currentThread().interrupt();
        System.out.println("是否停止1？" + Thread.interrupted());
        System.out.println("是否停止2？" + Thread.interrupted());
        System.out.println("end");
    }
}
```
运行结果：

```
begin
是否停止1？true
是否停止2？false
end
```
分析：interrupted方法的确判断出当前线程是否停止状态。
为什么第二个会是false呢？因为interrupted有解除功能，线程的中断由interrupted方法解除。所以两次连续调用则第二次返回false。

isInterrupted()方法

```
public class IsInterruptThread {
    public static void main(String[] args) {
        System.out.println("begin");
        Thread.currentThread().interrupt();
        System.out.println("是否停止1？" + Thread.currentThread().isInterrupted());
        System.out.println("是否停止2？" + Thread.currentThread().isInterrupted());
        System.out.println("end");
    }
}
```
执行结果：

```
begin
是否停止1？true
是否停止2？true
end
```
分析：能判断线程是否是中断状态，但连续调用不清除状态标志。

总结：
interrupted方法测试当前线程是否是中断状态，执行后具有将状态标志清除为false的功能；
isInterrupted方法测试当前线程是否是中断状态，但不清除状态标志。

### 能停止的线程——异常法

```
public class InterruptThread extends Thread {

    @Override
    public void run() {
        super.run();
        for (int i = 0; i < 500000; i ++ ) {
            if (this.isInterrupted()) {
                // 已经是中断状态了，要退出了！
                System.out.println("已经是中断状态了，要退出了！");
                break;
            }
            System.out.println("i = " + (i + 1));
        }
    }

    public static void main(String[] args) throws InterruptedException {
        InterruptThread interruptThread = new InterruptThread();
        interruptThread.start();
        sleep(1000);
        interruptThread.interrupt();
    }
}

```
运行结果：

```
省略...
i = 117746
i = 117747
i = 117748
i = 117749
i = 117750
i = 117751
i = 117752
i = 117753
已经是中断状态了，要退出了！

```

### 在沉睡中停止

```
public class SleepBeforeInterrupt extends Thread{

    @Override
    public void run() {
        super.run();
        try {
            System.out.println("before:" + this.isInterrupted());
            Thread.sleep(200000);
            System.out.println("....");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("end:" + this.isInterrupted());
    }

    public static void main(String[] args) {
        SleepBeforeInterrupt sleepBeforeInterrupt = new SleepBeforeInterrupt();
        sleepBeforeInterrupt.start();
        sleepBeforeInterrupt.interrupt();
    }
}
```
运行结果：

```
java.lang.InterruptedException: sleep interrupted
	at java.lang.Thread.sleep(Native Method)
	at chapter1.SleepBeforeInterrupt.run(SleepBeforeInterrupt.java:10)
before:true
end:false
```
分析：如果在sleep状态下停止某一状态，会进入catch语句，并且清除停止状态值，使之变成false。
简单说：先执行interrupt再运行sleep会出现异常。

### 能停止的线程——暴力停止
使用stop()停止线程是非常暴力的。

### 方法stop()与java.lang.ThreadDeath异常
stop方法已被作废，强制让线程停止有可能使一些清理性的工作得不到完成。另一情况就是对锁定的对象进行了“解锁”，导致数据得不到同步的处理，出现数据不一致的问题。

### 释放锁的不良后果
使用stop()释放锁将会给数据造成不一致性的后果。


### 使用return停止线程
将方法interrupt和return结合使用也能实现停止线程的效果

```
public class InterruptReturn extends Thread {

    @Override
    public void run() {
        super.run();
        while (true) {
            if (this.isInterrupted()) {
                System.out.println("执行interrupted了");
                return;
            }
        }
    }

    public static void main(String[] args) {
        InterruptReturn interruptReturn = new InterruptReturn();
        interruptReturn.start();
        try {
            Thread.sleep(2000);
            interruptReturn.interrupt();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

```
运行结果：

```
执行interrupted了
```

# 暂停线程
暂停线程意味着暂停后还可以恢复运行；
经常使用suspend()方法暂停，用resume()方法恢复线程的执行。
## suspend和resume方法的使用

```
public class SuspendResume extends Thread {

    private int i;

    @Override
    public void run() {
        super.run();
        while (true) {
            i ++;
        }
    }

    public int getI() {
        return i;
    }

    public void setI(int i) {
        this.i = i;
    }

    public static void main(String[] args) {
        try {
            SuspendResume suspendResume = new SuspendResume();
            suspendResume.start();
            Thread.sleep(5000);

            suspendResume.suspend();
            System.out.println("A:" + System.currentTimeMillis() + " i=" + suspendResume.getI());
            suspendResume.resume();
            Thread.sleep(5000);

            suspendResume.suspend();
            System.out.println("A:" + System.currentTimeMillis() + " i=" + suspendResume.getI());
            suspendResume.resume();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

```
输出：

```
A:1555760257178 i=-2049930246
A:1555760262182 i=506892209
```
## suspend和resume方法的缺点——独占

## suspend和resume方法的缺点——不同步

# yield()方法
yield方法的作用是放弃当前的CPU资源，让给其他任务区占用CPU执行时间，但放弃的时间不确定，有可能刚放弃又重新获得CPU时间片。

```
public class YieldThread extends Thread {

    @Override
    public void run() {
        super.run();
        int count = 0;
        long begin = System.currentTimeMillis();
        System.out.println("开始时间：" + begin);
        for (int i = 0; i < 5000000; i++) {
        	// Thread.yield();
            count = count + i;
        }
        long end = System.currentTimeMillis();
        System.out.println("结束时间：" + end);
        System.out.println("相差：" + (end - begin));
    }

    public static void main(String[] args) {
        YieldThread yieldThread = new YieldThread();
        yieldThread.start();
    }
}

```
运行结果：
1⃣️不用yield方法
```
开始时间：1555760862508
结束时间：1555760862515
相差：7
```
2⃣️用yield，打开注释
```
开始时间：1555760940292
结束时间：1555760945941
相差：5649
```
# 线程的优先级
在操作系统中，线程可以划分优先级，优先级较高的线程得到的CPU的资源较多，也就是CPU优先执行优先级较高的线程对象中的任务。
设置线程的优先级有助于帮“线程规划器”确定下一次选择哪个线程来优先执行。
设置线程的优先级使用setPriority()方法

JDK源码：
```
public final void setPriority(int newPriority) {
        ThreadGroup g;
        checkAccess();
        if (newPriority > MAX_PRIORITY || newPriority < MIN_PRIORITY) {
            throw new IllegalArgumentException();
        }
        if((g = getThreadGroup()) != null) {
            if (newPriority > g.getMaxPriority()) {
                newPriority = g.getMaxPriority();
            }
            setPriority0(priority = newPriority);
        }
    }
```
优先级分为1-10。
## 线程优先级的继承特性
线程的优先级具有继承性。例如：A线程启动B线程，则B的优先级和A是一样的。

## 优先级具有规则性
线程的优先级具有规则性，即CPU会尽量将执行资源分配给优先级高的线程。
高优先级的线程总是大部分执行完，但不保证高优先级的线程全部先执行完。

## 优先级具有随机性
优先级高的不一定最先执行完。

## 优先级高的运行得快

# 守护线程
在Java中线程分两种，一种是用户线程，一种是守护线程（daemon）。
守护线程：是一种特殊的线程，它的特性有陪伴的意思，当进程中不存在非守护线程时，守护线程自动销毁。典型的是垃圾回收线程。

