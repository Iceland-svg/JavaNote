## 1 Thread提供的其他功能

### 1.1 线程命名
创建线程时可以这样写---Thread（String name）

```java
public class Test4 {  
    public static void main(String[] args) {  
        Thread thread = new Thread(()->{  
            while (true) {  
                System.out.println("666");  
            }  
        },"我的线程");  
        while (true) {  
            System.out.println("哈哈哈");  
        }  
    }  
}
```

### 1.2 isAilve（）方法判断线程存活

直接对某个线程调用isAilve（）返回true，false，本质上是判断是否在执行
```java
thread.isAlive()
```

---

## 2 什么是前台线程?什么是后台线程?

**前台线程**：==这个线程没执行完，进程就不会结束==
**后台线程**：这个线程没运行完，进程可以结束，无法阻止进程结束

自己创建的线程==默认是前台线程==
如果在start之前也可以通过setDaemon（）来修改为后台线程
JVM垃圾回收就是后台线程（周期性执行）

---

## 3 如何启动线程- start（）

一个线程对象只能start一次，start之后线程处于就绪/阻塞状态，对于就绪/阻塞状态的线程不能start

---

## 4 线程是怎么中断的?

（1）运用变量标志位

```java
private static boolean flag = true;
    public static void main(String[] args) throws InterruptedException{
        Thread t = new Thread(()->{
            while (flag){
                System.out.println("正在执行线程任务");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        t.start();

        System.out.println("正在执行主线程任务,执行后休息3秒");
        Thread.sleep(3000);
        flag = false;
        System.out.println("3秒后t线程终止");
    }
```

（2）运用线程内置标志位 isInterrupted（）

```java
public static void main(String[] args) throws InterruptedException{
        Thread t = new Thread(()->{
            //通过Thread.CurrentThread()来获取当前线程的引用
            while (!Thread.currentThread().isInterrupted()){
                //线程不处于中断状态就执行
                System.out.println("正在执行线程任务");
            }
        });

        t.start();

        System.out.println("正在执行主线程任务");

        Thread.sleep(3000);

        t.interrupt();//利用interrupt()让线程终止

        System.out.println("t线程终止");
    }
```

（在lambda表达式内部用t时，此时t还未被创建，需要用Thread.currentThread获取该线程的引用，在那个线程里调用获取的就是那个线程的引用）

（3）为什么支持提前唤醒正在sleep的线程?
当sleep被提前终止时会抛出iterruptException异常
同时把isInterrupted（）返回值改为false
```java
public static void main(String[] args) throws InterruptedException{
        Thread t = new Thread(()->{
            //通过Thread.CurrentThread()来获取当前线程的引用
            while (!Thread.currentThread().isInterrupted()){
                //线程不处于中断状态就执行
                System.out.println("正在执行线程任务");
                //如果让t线程处于sleep状态
                //此时如果在main里调用interrupt(),那么sleep会被提前唤醒
                //此时触发了异常就会把isInterrupt()标志为置为false
                //导致线程继续执行
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });

        t.start();

        System.out.println("正在执行主线程任务");

        Thread.sleep(3000);

        t.interrupt();//利用interrupt()让线程终止

        System.out.println("t线程终止");
    }
```

主要目的是为了给程序员更多的操作空间，因为sleep还没睡够就被唤醒了，是否会存在一些未完成工作？这里就需要交给程序员考虑

sleep被提前唤醒的异常的三种处理方式

1 立刻终止
2忽略
3 添加善后逻辑，稍后结束

---

## 5 我们为什么需要线程等待?

操作系统在调度线程的时候是随机的（抢占式执行），作为程序而言通常是不喜欢随机的，所以这个时候我们需要通过join（）方法来让要后结束的线程阻塞，等待要先结束的线程，执行完

```java
public static void main(String[] args) throws InterruptedException{
        Thread t = new Thread(()->{
            for (int i = 0; i < 5; i++) {
                System.out.println("正在执行线程任务");
            }
        });

        t.start();

        System.out.println("主线程等待之前");
        //执行到这里才开始阻塞
        t.join();
        //此时就是让t线程先执行完，然后让main线程等待
        //t.join后面的代码处于阻塞状态
        System.out.println("主线程等待之后");

    }
```

谁调join（），谁先结束，另一个必须等待

--join（）有一个重载版本，可以指定等待的最大时间

---

## 6 线程状态
### （1）new状态

Thread对象创建了，但是还没start

### （2）terminated状态

线程执行完了，Thread对象还在
这里注意操作系统中线程的生命周期和Thread对象的不一致
### （3）Runnable状态

只要不触发阻塞都是Runnable状态

### （4）BLOCKE状态

加锁产生的阻塞
### （5）WATING状态

无时间的阻塞
### （6）TIME_WATING状态

有时间的阻塞，join有参数版本或者sleep
```java
private static Thread mainThread ;
    public static void main(String[] args) throws InterruptedException{
        Thread t = new Thread(()->{
            for (int i = 0; i < 5; i++) {
                System.out.println("正在执行线程任务");
                }
        });

        Thread mainThread = Thread.currentThread();

        System.out.println(t.getState()+" 线程存活状态；"+t.isAlive());

        t.start();
        //代码中不触发阻塞就都是Runnable状态
        System.out.println(t.getState()+" 线程存活状态；"+t.isAlive());

        t.join();
        //t线程执行完了，但是Thread对象还在，此时是terminated状态
        System.out.println(t.getState()+" 线程存活状态；"+t.isAlive());

        Thread.sleep(6000);

        System.out.println(mainThread.getState()+" 线程存活状态；"+mainThread.isAlive());
    }
```

此时的运行窗口
![](assets/2%20线程创建和基础用法/file-20260309220812199.png)
