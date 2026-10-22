## 1 callable
运用例子

---

## 2 补充问题
创建线程的方式有哪几种?
1 继承Thread类
2  实现Runnable接口
3 lambda表达式（实际上也是Runnable）
4 callable
5  基于线程池

---

## 3 reentrantlock——比较传统的锁

### synchronized和reentrantlock的区别是什么?

（1） synchronized拿不到锁就会死等
reentrantlock拿不到可以通过trylock方法尝试一段时间就放弃

运用实例

（2） synchronized是非公平锁，reentrantlock是可以在括号里写上true设置为公平锁

（3） synchronized 是通过wait/notify实现等待唤醒reentrantlock是搭配condition类来实现，更精准的控制唤醒某个线程

（4） synchronized是关键字，是jvm内部实现的（），reentrantlock是Java中的类，是Java实现的

（5） synchronized不需要主动去释放锁，但是reentrantlock需要通过unlock来释放锁，更灵活
信号量，相当于锁的延伸，锁相当于信号量资源为1

代码运用
CountDownLatch
运用例子
多线程环境使用哈希表如何保证线程安全?

ConcurrentHashMap-经典面试题
HashMap，HashTable，和ConcurrentHahMap