# 如何了解JUC

**源码 + 官方文档**（面试高频问

![1589277382875](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589277382875.png)

java.util 工具包，分类

**Runnable** 没有返回值，效率相比Callable相对较低

> java默认有两个线程；

**Java本身不能开启线程**

```java
public synchronized void start() {
        /**
         * This method is not invoked for the main method thread or "system"
         * group threads created/set up by the VM. Any new functionality added
         * to this method in the future may have to also be added to the VM.
         *
         * A zero status value corresponds to state "NEW".
         */
        if (threadStatus != 0)
            throw new IllegalThreadStateException();

        /* Notify the group that this thread is about to be started
         * so that it can be added to the group's list of threads
         * and the group's unstarted count can be decremented. */
        group.add(this);

        boolean started = false;
        try {
            start0();
            started = true;
        } finally {
            try {
                if (!started) {
                    group.threadStartFailed(this);
                }
            } catch (Throwable ignore) {
                /* do nothing. If start0 threw a Throwable then
                  it will be passed up the call stack */
            }
        }
    }

    private native void start0();

    /**
     * If this thread was constructed using a separate
     * <code>Runnable</code> run object, then that
     * <code>Runnable</code> object's <code>run</code> method is called;
     * otherwise, this method does nothing and returns.
     * <p>
     * Subclasses of <code>Thread</code> should override this method.
     *
     * @see     #start()
     * @see     #stop()
     * @see     #Thread(ThreadGroup, Runnable, String)
     */
    @Override
    public void run() {
        if (target != null) {
            target.run();
        }
    }
	//本地方法，底层是C++,java无法直接操作硬件
	private native void start0();
```



# 线程/进程

> 线程有几个状态

```java
public enum State {
       //新生
        NEW,

       //运行
        RUNNABLE,

       //阻塞
        BLOCKED,

      //等待，死死等待
        WAITING,

       //超时等待
        TIMED_WAITING,

        //终止
        TERMINATED;
    }
```



> wait和sleep的区别

1. **来自不同的类**

   * wait ：Object
   * sleep：Thread

2. **关于锁的释放**

   wait会释放锁，sleep睡觉了，不释放锁

3. **使用的范围是不同的**

   wait：必须在同步代码块中使用

   sleep：可以在任何地方

4. **是否需要捕获异常**

   * wait：不需要捕获异常
   * sleep：需要捕获异常

## Lock锁（重点

> 传统synchronized

本质是：队列



> Lock 接口

![1589281537332](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589281537332.png)



![1589281569868](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589281569868.png)

![1589281629142](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589281629142.png)

* 公平锁：十分公平，可以先来后到
* **非公平锁：十分不公平，可以插队(默认)**

> Lock三部曲

1. new ReentrantLock()
2. lock.lock()
3. finally=> lock.unlock()//解锁

> synchronized和Lock锁的区别

* synchronized 内置的java关键字，Lock是一个Java类
* synchronized 无法判断获取锁的状态，Lock可以判断获取到锁的状态
* synchronized 会自动释放锁，Lock必须手动释放锁，如果不释放会死锁
* synchronized 线程1获取锁，线程2会一直等待。Lock不一定会等待下去
* synchronized 可重入锁，不可中断，非公平。Lock，可重入锁，可以判断锁，非公平(可以自己设置)
* synchronized 适合锁少量的代码同步问题，Lock适合锁大量的同步代码

> 生产者，消费者：判断等待，业务，通知

![1589386074811](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589386074811.png)

```java
package com.chen.pc;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/14 0:10
 * @Version V1.0
 **/

public class OldLock {

    public static void main(String[] args) {

        Data1 data = new Data1();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try{
                    data.increament();
                }catch (Exception e){
                    e.printStackTrace();
                }
            }
        },"A").start();


        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try{
                    data.increament();
                }catch (Exception e){
                    e.printStackTrace();
                }
            }
        },"B").start();


        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try{
                    data.decrement();
                }catch (Exception e){
                    e.printStackTrace();
                }
            }
        },"C").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try{
                    data.decrement();
                }catch (Exception e){
                    e.printStackTrace();
                }
            }
        },"D").start();

    }
}

class Data1{
    private int number = 0;

    public synchronized void increament() throws Exception{
            //业务代码
            while (number!= 0){
                //等待
                this.wait();
            }
            number++;
            System.out.println(Thread.currentThread().getName()+"=>"+number);
            //通知其他线程我+1完毕了
            this.notifyAll();
    }

    public synchronized void decrement() throws Exception{
            //业务代码
            while (number == 0){
                this.wait();
            }
            number--;
            System.out.println(Thread.currentThread().getName()+"=>"+number);
            //通知其他线程我减一完毕了
            this.notifyAll();
    }
}
```

业务代码需要在每次循环进入的适合等待，不能用<kbd>if</kbd>

> JUC版的生产者消费者问题

通过Lock找到Condition

![1589386099207](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589386099207.png)

```java
package com.chen.pc;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/14 0:10
 * @Version V1.0
 **/

public class jucPC {

    public static void main(String[] args) {

        final Data data = new Data();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try{
                    data.increament();
                }catch (Exception e){
                    e.printStackTrace();
                }
            }
        },"A").start();


        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try{
                    data.increament();
                }catch (Exception e){
                    e.printStackTrace();
                }
            }
        },"B").start();


        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try{
                    data.decrement();
                }catch (Exception e){
                    e.printStackTrace();
                }
            }
        },"C").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try{
                    data.decrement();
                }catch (Exception e){
                    e.printStackTrace();
                }
            }
        },"D").start();

    }
}

class Data{
    private int number = 0;

    Lock lock = new ReentrantLock();
    //添加锁的监视器对象
    Condition condition = lock.newCondition();

    public void increament(){
        lock.lock();
        try{
            //业务代码
            while (number!= 0){
                //等待
                condition.await();
            }
            number++;
            System.out.println(Thread.currentThread().getName()+"=>"+number);
            //通知其他线程我+1完毕了
            condition.signalAll();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }

    public void decrement(){
        lock.lock();
        try{
            //业务代码
            while (number == 0){
                condition.await();
            }
            number--;
            System.out.println(Thread.currentThread().getName()+"=>"+number);
            //通知其他线程我减一完毕了
            condition.signalAll();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
}





```

![1589387079461](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589387079461.png)

> Condtion 精准的通知和唤醒线程

```java
package com.chen.pc;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/14 0:30
 * @Version V1.0
 **/

/***
 * A执完调用B
 * B执行完调用C
 * C执行完调用A
 */

public class ConditionLock {

    public static void main(String[] args) {

        Data3 data3 = new Data3();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                data3.printA();
            }
        },"A").start();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                data3.printB();
            }
        },"B").start();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                data3.printC();
            }
        },"C").start();
    }
}


class Data3{
    //资源类

    private Lock lock = new ReentrantLock();
    private Condition condition1 =lock.newCondition();
    private Condition condition2 = lock.newCondition();
    private Condition condition3 = lock.newCondition();

    private int number = 1;

    public void printA(){
        lock.lock();
        try{
            //业务,判断，执行 通知
            while (number != 1){
                //等待
                condition1.await();
            }
            System.out.println(Thread.currentThread().getName()+"=>AAAAAAAA");
            //唤醒指定的 人
            number = 2;
            condition2.signal();

        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }

    }

    public void printB(){

        lock.lock();
        try{
            //业务,判断，执行 通知
            while (number != 2){
                //等待
                condition2.await();
            }
            System.out.println(Thread.currentThread().getName()+"=>BBBBBB");
            //唤醒指定的 人
            number = 3;
            condition3.signal();

        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }

    }

    public void printC(){

        lock.lock();
        try{
            //业务,判断，执行 通知
            while (number != 3){
                //等待
                condition3.await();
            }
            System.out.println(Thread.currentThread().getName()+"=>CCCCCC");
            //唤醒指定的 人
            number = 1;
            condition1.signal();

        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }

    }

}
```

![1589387978920](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589387978920.png)



# 锁现象

锁的是谁！锁到底锁的是谁~



> 小结

new this 具体的一个收集

static Class 唯一的一个模板

# 集合类不安全

> List不安全

```java
package com.chen.unsafe;

import java.util.*;
import java.util.concurrent.CopyOnWriteArrayList;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/14 1:08
 * @Version V1.0
 **/
//并发修改异常
public class ListTest {
    public static void main(String[] args) {
//        List<String> list = Arrays.asList("1","2","3");
//        list.forEach(System.out::println);

        /**
         * 1.List<String> list = new Vector()
         * 2.  List<String> list = Collections.synchronizedList(new ArrayList<>());
         * 3. List<String> list = new CopyOnWriteArrayList<>();
         */
        //CopyOnWrite写入时 复制COW

        List<String> list = new CopyOnWriteArrayList<>();
        for (int i = 0; i < 10; i++) {
            new Thread(()->{
                list.add(UUID.randomUUID().toString().substring(0,5));
                System.out.println(list);
            },String.valueOf(i)).start();
        }

    }
}

```

> set

```java
package com.chen.unsafe;

import java.util.Collections;
import java.util.HashSet;
import java.util.Set;
import java.util.UUID;
import java.util.concurrent.CopyOnWriteArraySet;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/15 0:27
 * @Version V1.0
 **/

/**
 * 同理可证
 */

public class SetTest {
    public static void main(String[] args) {
//        Set<String> set = new HashSet<>();
//        Set<String> set = Collections.synchronizedSet(new HashSet<>());
        Set<String> set = new CopyOnWriteArraySet();

        for (int i = 0; i < 10; i++) {
            new Thread(()->{
                set.add(UUID.randomUUID().toString().substring(0,5));
                System.out.println(set);
            },String.valueOf(i)).start();
        }
    }
}

```

> HashMap 不安全

![1589475017105](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589475017105.png)

```java
package com.chen.unsafe;

import java.util.Collections;
import java.util.HashMap;
import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ConcurrentHashMap;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/15 0:38
 * @Version V1.0
 **/

public class MapTest {
    public static void main(String[] args) {
        //默认等价于什么
//        Map<String,String> map = new HashMap<String, String>(16, (float) 0.75);

        Map<String,String> map = new ConcurrentHashMap<>();

        //加载因子，初始化容量
        for (int i = 0; i < 30; i++) {
            new Thread(()->{
                map.put(Thread.currentThread().getName(), UUID.randomUUID().toString().substring(0,5));
                System.out.println(map);
            },String.valueOf(i).toString()).start();

        }
    }
}

```

## Callable(简单)

![1589476156499](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589476156499.png)

1. 可以有返回值
2. 可以抛出异常
3. 方法不同 run/call



```java
package com.chen.callable;

import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/15 1:10
 * @Version V1.0
 **/

public class callableTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        new Thread().start();

        MyThread myThread = new MyThread();
        FutureTask futureTask = new FutureTask(myThread);   //适配器类

        new Thread(futureTask,"A").start();

        Integer o = (Integer)futureTask.get();//获取callable的返回结果

        System.out.println(o);
    }
}

class MyThread implements Callable<Integer> {

    @Override
    public Integer call() throws Exception {
        return 11;
    }
}
```

**细节：**

1. 有缓存
2. 结果可能需要等待,会阻塞!

# 常用的辅助类

### CountDownLatchDemo

```java
package com.chen.add;

import java.util.concurrent.CountDownLatch;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/15 13:53
 * @Version V1.0
 **/

public class CountDownLatchDemo {

    public static void main(String[] args) throws InterruptedException {
        //总数是6,必须要执行的任务的时候，再使用！
        CountDownLatch countDownLatch = new CountDownLatch(6);

        for (int i = 0; i < 6; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName()+"Go out");
                countDownLatch.countDown();
            },String.valueOf(i).toString()).start();
        }

        //等待计数器归零，然后再向下执行
        countDownLatch.await();

        System.out.println("Close Door");

    }

}

```

原理：

* countDownLatch.countDown(); //数量减一
* countDownLatch.await();//等待计数器归零

每次有线程调用的时候countDown()数量减一，假设计数器变为0，countDownLatch.await();就会被唤醒,继续执行！

### CyclicBarrier

```java
package com.chen.add;

import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/15 14:01
 * @Version V1.0
 **/

public class CyclicBarrierDemo {
    public static void main(String[] args) {
        /**
         * 集齐7颗龙珠召唤神龙
         */

        //召唤龙珠的线程
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7,()->{
            System.out.println("召唤神龙成功!!");
        });




        for (int i = 0; i < 7; i++) {
            final int temp = i;
            new Thread(()->{
                //lambda能操作到i吗
                System.out.println(Thread.currentThread().getName()+" 收集"+ temp+"个龙珠");
                try {
                    cyclicBarrier.await();// 等待
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }

            }).start();
        }

    }
}

```

### Semaphore

```java
package com.chen.add;

import java.sql.Time;
import java.util.concurrent.Semaphore;
import java.util.concurrent.TimeUnit;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/15 14:06
 * @Version V1.0
 **/

public class semaphoreDemo {

    public static void main(String[] args) {
        //线程数量: 停车位
        Semaphore semaphore = new Semaphore(3);

        for (int i = 0; i <= 6; i++) {
            new Thread(()->{
                // acquire()  得到
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName()+"抢到车位");
                    TimeUnit.SECONDS.sleep(2);
                    System.out.println(Thread.currentThread().getName()+"离开车位");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }finally {
                    //release()  释放
                    semaphore.release();
                }
            },String.valueOf(i)).start();
        }
    }
}

```

**原理：**

`semaphore.acquire()`获得，假设如果已经满了，等待，等待被释放~

` semaphore.release()` 释放，会将当前的信号量释放+1，然后唤醒等待的线程！

共享资源互斥的使用!并发限流，控制最大的线程数

# 读写锁

![1589523370670](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589523370670.png)

```java
package com.chen.rw;

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/15 14:16
 * @Version V1.0
 **/

/**
 *   独占锁 （写锁）   一次只能被一个线程占有
 *   共享锁   （读锁） 多个线程可以同时占有
 *  ReadWriteLock
 *  读-读 可以共存
 *  读-写 不能共存
 *  写-写 不能共存
 */

public class ReadWriteLockDemo {

    public static void main(String[] args) {
        MyCache myCache  = new MyCache();

        //写入
        for (int i = 0; i < 5; i++) {
            final  int temp = i;
            new Thread(()->{
                myCache.put(temp+"",temp+"");
            },String.valueOf(i)).start();
        }

        //读取
        for (int i = 0; i < 5; i++) {
            final  int temp = i;
            new Thread(()->{
                myCache.get(temp+"",temp+"");
            },String.valueOf(i)).start();
        }

    }

}

/**
 * 加锁的缓存
 */

class MyCacheLock{

    private volatile Map<String,Object> map = new HashMap<>();
    // 读写锁:更加细粒度的控制
    private ReadWriteLock readWriteLock = new ReentrantReadWriteLock();

    //存   写入的时候，只希望有一个线程写
    public void put(String key,Object value){
        readWriteLock.writeLock().lock();
      try{
          System.out.println(Thread.currentThread().getName()+"写入"+key);
          map.put(key,value);
          System.out.println(Thread.currentThread().getName()+"写入ok");

      }catch (Exception e){
          e.printStackTrace();
      }finally {
          readWriteLock.writeLock().unlock();
      }
    }

    //取   读
    public void get(String key){

        readWriteLock.readLock().lock();

       try{
           System.out.println(Thread.currentThread().getName()+"读取"+key);
           Object o = map.get(key);
           System.out.println(Thread.currentThread().getName()+"读取ok");
       }catch (Exception e){
           e.printStackTrace();
       }finally {
           readWriteLock.readLock().unlock();
       }
    }
}




/**
 * 自定义缓存
 */
class MyCache{

    private volatile Map<String,Object> map = new HashMap<>();

    //存   写
    public void put(String key,Object value){
        System.out.println(Thread.currentThread().getName()+"写入"+key);
        map.put(key,value);
        System.out.println(Thread.currentThread().getName()+"写入ok");
    }

    //取   读
    public void get(String key){
        System.out.println(Thread.currentThread().getName()+"读取"+key);
        Object o = map.get(key);
        System.out.println(Thread.currentThread().getName()+"读取ok");
    }
}

```

# 阻塞队列

阻塞：

队列：

![1589524325639](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589524325639.png)

**BlockingQueue**：多线程并发处理，线程池! 

![1589524720792](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589524720792.png)

**学会使用队列：四组API**

1.  抛出异常
2. 不会抛出异常
3. 阻塞 等待
4. 超时等待



| 方式         | 抛出异常 | 有返回值 | 阻塞 等待 | 超时等待  |
| ------------ | -------- | -------- | --------- | --------- |
| 添加         | add      | offer    | put       | offer(,,) |
| 移除         | remove   | poll     | take      | poll(),   |
| 检测队首元素 | element  | peek     |           |           |

**抛出异常**

```java
 /**
     * 抛出异常
     */
    public static void test1(){
        ArrayBlockingQueue blockingQueue = new ArrayBlockingQueue<>(3);

       System.out.println( blockingQueue.add("a"));
        System.out.println( blockingQueue.add("b"));
        System.out.println( blockingQueue.add("c"));

        //队列慢了抛出异常:java.lang.IllegalStateException:Queue full
//        System.out.println( blockingQueue.add("d"));
        System.out.println("=========================================");

        System.out.println(blockingQueue.remove());
        System.out.println(blockingQueue.remove());
        System.out.println(blockingQueue.remove());

        //抛出异常：NoSuchElementException 
        System.out.println(blockingQueue.remove());

    }
```

**有返回值没有异常**

```java
/**
     * 有返回值没有异常
     */
    public  static void test2(){
        ArrayBlockingQueue arrayBlockingQueue = new ArrayBlockingQueue(3);

        System.out.println(arrayBlockingQueue.offer("a"));
        System.out.println(arrayBlockingQueue.offer("b"));
        System.out.println(arrayBlockingQueue.offer("c"));

//        System.out.println(arrayBlockingQueue.offer("d"));//false 不抛出异常

        System.out.println(arrayBlockingQueue.poll());
        System.out.println(arrayBlockingQueue.poll());
        System.out.println(arrayBlockingQueue.poll());

//        System.out.println(arrayBlockingQueue.poll());//null

    }
```

**阻塞 等待**

```java
 /**
     * 等待，阻塞（一直
     */
    public static void test3() throws InterruptedException {
        ArrayBlockingQueue blockingQueue = new ArrayBlockingQueue(3);

        //一直阻塞
        blockingQueue.put("a");
        blockingQueue.put("b");
        blockingQueue.put("c");
        //队列没有位置
//        blockingQueue.put("d");

        System.out.println(blockingQueue.take());
        System.out.println(blockingQueue.take());
        System.out.println(blockingQueue.take());
        System.out.println(blockingQueue.take());//没有这个元素

    }
```

**阻塞 超时等待，实践限制**

```java
/**
     * 等待，阻塞（有计时
     */
    public static void test4() throws InterruptedException {
        ArrayBlockingQueue blockingQueue = new ArrayBlockingQueue(3);

        blockingQueue.offer("a");
        blockingQueue.offer("b");
        blockingQueue.offer("c");
        blockingQueue.offer("d",2, TimeUnit.SECONDS);// 等待超过两秒就退出了

        System.out.println("=======================================");
        blockingQueue.poll();
        blockingQueue.poll();
        blockingQueue.poll();
        blockingQueue.poll(2,TimeUnit.SECONDS);

    }
```

# 线程池

> 池化技术

**线程池的好处**

1. 降低资源的消耗
2. 提高响应的速度
3. 方便管理

==线程复用，可以控制最大的并发数，管理线程==



> 线程池：三大方法，

```java
package com.chen.pool;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/15 15:17
 * @Version V1.0
 **/
//使用线程池来创建线程
public class Demo01 {
    public static void main(String[] args) {
//        ExecutorService threadPool = Executors.newSingleThreadExecutor();// 单个线程
//        ExecutorService threadPool = Executors.newFixedThreadPool(5);//创建一个固定大小的线程池
        ExecutorService threadPool = Executors.newCachedThreadPool();//可伸缩的线程池

        //程序结束要关闭线程池
        try{
            for (int i = 0; i < 10; i++) {
                threadPool.execute(()->{
                    System.out.println(Thread.currentThread().getName()+"ok");
                });
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            threadPool.shutdown();
        }

    }
}
```

> 七大参数

```java
public static ExecutorService newSingleThreadExecutor() {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>()));
    }

 public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
    }

public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>());
    }

//本质是：ThreadPoolExecutor()
 
public ThreadPoolExecutor(int corePoolSize,		//核心线程池大小
                              int maximumPoolSize,//最大线程池大小
                              long keepAliveTime,//超时没有人调用就会释放
                              TimeUnit unit,//超时单位
                              BlockingQueue<Runnable> workQueue,//阻塞队列
                              ThreadFactory threadFactory,//线程工厂：创建线程，一般不用动
                              RejectedExecutionHandler handler) {//拒绝策略
        if (corePoolSize < 0 ||
            maximumPoolSize <= 0 ||
            maximumPoolSize < corePoolSize ||
            keepAliveTime < 0)
            throw new IllegalArgumentException();
        if (workQueue == null || threadFactory == null || handler == null)
            throw new NullPointerException();
        this.acc = (System.getSecurityManager() == null)
            ? null
            : AccessController.getContext();
        this.corePoolSize = corePoolSize;
        this.maximumPoolSize = maximumPoolSize;
        this.workQueue = workQueue;
        this.keepAliveTime = unit.toNanos(keepAliveTime);
        this.threadFactory = threadFactory;
        this.handler = handler;
    }

```



![1589527894647](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589527894647.png)

> 手动创建一个线程池

```java
package com.chen.pool;

import java.util.concurrent.*;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/15 15:32
 * @Version V1.0
 **/

public class Demo02 {
    public static void main(String[] args) {
        //自定义线程。
        /**
         * 最大线程到底该如何定义
         * 1.CPU： 密集型，几核，就是几。可以保持CPU的效率最高
         * 2.IO 密集型->判断你的程序十分耗IO的线程
         *       程序: 15个大型任务,io 十分占用资源！
         */

        //获取CPU的核数
        System.out.println(Runtime.getRuntime().availableProcessors());

        ExecutorService threadPool = new ThreadPoolExecutor(2,5,3, TimeUnit.SECONDS,new LinkedBlockingDeque<>(3)
        , Executors.defaultThreadFactory(),new ThreadPoolExecutor.AbortPolicy());//可伸缩的线程池
        //最后一个参数的策略是，银行满了，还有人进来，就不处理这个人的，抛出异常

        //程序结束要关闭线程池
        try{
            for (int i = 1; i < 5; i++) {
                threadPool.execute(()->{
                    System.out.println(Thread.currentThread().getName()+"ok");
                });
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            threadPool.shutdown();
        }

    }
}


```

>小结和扩展

池的最大的大小如何去设置!

了解：IO密集型，CPU密集型：（调优

# 四大函数式接口

**代码测试：**

函数式接口:

```java
package com.chen.function;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/15 16:38
 * @Version V1.0
 **/

import java.util.function.Function;

/**
 * 函数型接口
 */

public class Demo01 {
    public static void main(String[] args) {
        //工具类：输出输入的值
//        Function<String,String> function = new Function<String,String>(){
//            @Override
//            public String apply(String o) {
//                return o;
//            }
//        };

        Function<String,String> function = (str) ->{return  str;};
        System.out.println(function.apply("hello"));
    }
}

```

> 断定型接口

```java
package com.chen.function;

import java.util.function.Predicate;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/15 16:43
 * @Version V1.0
 **/

/**
 *  断定型接口:有一个输入参数，返回值只能是 布尔值！
 */
public class Demo02 {
    public static void main(String[] args) {
//        Predicate<String> predicate = new Predicate<String>() {
//            //判断字符串是否为空
//            @Override
//            public boolean test(String s) {
//                return s.isEmpty();
//            }
//        };

        Predicate<String> predicate = (str)->{ return str.isEmpty();};

        System.out.println(predicate.test("asd"));
    }
}

```

>  Consumer 消费型接口

```java
package com.chen.function;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/15 16:49
 * @Version V1.0
 **/

import java.util.function.Consumer;

/**
 * Consumer 消费型接口：只有输入，目要返回值
 */
public class Demo03 {
    public static void main(String[] args) {
//        Consumer<String> consumer = new Consumer<String>(){
//            @Override
//            public void accept(String str) {
//                System.out.println(str);
//            }
//        };
        Consumer<String> consumer = (str) ->{System.out.println(str);};

        consumer.accept("hello");
    }
}

```

> 供给型接口

```java
package com.chen.function;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/15 16:52
 * @Version V1.0
 **/

import java.util.function.Supplier;

/**
 * Supplier共给型接口，没有参数，只有返回值
 */
public class Demo04 {
    public static void main(String[] args) {
//        Supplier supplier = new Supplier<Integer>() {
//            @Override
//            public Integer get() {
//              System.out.println("get()");
//              return 1024;
//            }
//        };

        Supplier supplier = ()->{return 1024;};
      System.out.println(supplier.get());
    }
}

```

==要掌握的：泛型，枚举，反射；lambda表达式，链式表达式，函数式接口，Stream流式计算==



# Stream流式计算

大数据:存储+计算

计算都可以交给流来操作

```java
package com.chen.stream;

import java.util.Arrays;
import java.util.List;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/15 17:31
 * @Version V1.0
 **/

/**
 * 题目要求：以分组内完成此题，只能用一行代码实现
 * 现有5个用户，筛选：
 * 1.ID必须是偶数
 * 2.年龄必须大于23岁
 * 3.用户名转换为大写字母
 * 4.用户名字母倒叙输出
 * 5.只能输出一个用户！
 */
public class Test {
    public static void main(String[] args) {
        User u1 = new User(1,"a",21);
        User u2 = new User(2,"b",22);
        User u3 = new User(3,"c",23);
        User u4 = new User(4,"d",24);
        User u5 = new User(6,"e",25);
        //集合就是存储
        List<User> list = Arrays.asList(u1,u2,u3,u4,u5);

        //计算交给流
        list.stream()
                .filter(u->{return u.getId()%2==0;})
                .filter(u->{return u.getAge()>23;})
                .map(u->{return u.getName().toUpperCase();})
                .sorted((uu1,uu2)->{return uu2.compareTo(uu1);})
                .limit(1)
                .forEach(System.out::println);
    }
}

```

# ForkJoin

![1589535900671](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589535900671.png)

> ForkJoin特点：工作窃取

> ForkJoin

![1589540831787](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589540831787.png)



```java
package com.chen.ForkJoin;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/15 17:47
 * @Version V1.0
 **/
//求和计算的任务
/**
 * 1. forkjoinPool  通过它来执行
 * 2. 计算任务 forkjoinPool.execute(ForkJoinTask tak)
 * 3. 计算类要继承 ForkJoinTask
 */

import java.util.concurrent.RecursiveTask;

/**
 * 如何使用forkjoin
 * 1.forkjoinpool 通过它来执行
 */
public class ForkJoinDemo extends RecursiveTask<Long> {

    private Long start;//1
    private Long end;//1990900000

    //零界值
    private Long temp = 10000L;

    public ForkJoinDemo(Long star,Long end){
        this.start = star;
        this.end = end;
    }

    public  void test() {
        if((end-start)>temp){
            //分支合并计算

        }else {
            int sum = 0;
            for (int i = 0; i < 10_0000_0000; i++) {
                sum += i;
            }
            System.out.println(sum);
        }
    }

    //计算的方法
    @Override
    protected Long compute() {
       if ((end-start)<temp){
           Long sum = 0L;
           for (Long i = start; i <= end; i++) {
                sum += i;
           }
           return sum;
       }else {//forkjoin 递归
            long middle = (start + end) / 2;// 中间值
           ForkJoinDemo task1 = new ForkJoinDemo(start, middle);
           task1.fork();//拆分任务，把任务压入线程队列
           ForkJoinDemo task2 = new ForkJoinDemo(middle+1, end);
           task2.fork();//拆分任务，把任务压入线程队列
           return task1.join() + task2.join();
       }
    }
}

```

```java
package com.chen.ForkJoin;

import java.util.concurrent.ExecutionException;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.ForkJoinTask;
import java.util.stream.LongStream;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/15 18:57
 * @Version V1.0
 **/

public class Test {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
//        test1();//8952
//        test2();
//        test3();295
    }

    //普通程序员
    public static void test1(){
        long start = System.currentTimeMillis();
        Long sum = 0L;
        for(Long i = 1L;i<10_0000_0000;i++){
            sum += i;
        }
        long end = System.currentTimeMillis();
        System.out.println("sum="+"时间"+(end-start));
    }

    //会使用ForkJoin
    public static void test2() throws ExecutionException, InterruptedException {
        long start = System.currentTimeMillis();

        ForkJoinPool forkJoinPool = new ForkJoinPool();
        ForkJoinDemo task= new ForkJoinDemo(0L, 10_0000_0000L);
        ForkJoinTask<Long> submit = forkJoinPool.submit(task);
        Long summer = submit.get();

        long end = System.currentTimeMillis();
        System.out.println("sum="+"时间"+(end-start));
    }

    public static void test3(){
        long start = System.currentTimeMillis();

        //并行流Stream() ]
        LongStream.rangeClosed(0L,10_0000_0000L).parallel().reduce(0,Long::sum);
        long end = System.currentTimeMillis();
        System.out.println("sum="+"时间"+(end-start));
    }
}

```

# 异步回调

> Future 设计的初衷：对将未来的某个事情结果进行建模



```java
package com.chen.future;

import java.util.concurrent.*;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/16 0:10
 * @Version V1.0
 **/

/**
 * 异步调用：CompletableFuture
 * 异步执行
 * 成功回调
 * 失败回调
 */

public class Demo01 {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        //发起一个请求
        //没有返回值的异步回调
//        CompletableFuture<Void> objectCompletableFuture = CompletableFuture.runAsync(()->{
//            try {
//                TimeUnit.SECONDS.sleep(2);
//            } catch (InterruptedException e) {
//                e.printStackTrace();
//            }
//            System.out.println(Thread.currentThread().getName()+"runAsync=>Void");
//        });

//        objectCompletableFuture.get();// 获取阻塞执行结果
        //有返回值的异步回调
        //ajax 成功和失败的回调
        CompletableFuture<Integer> completableFuture = CompletableFuture.supplyAsync(()->{
            System.out.println(Thread.currentThread().getName()+"supplyAsnc=>Integer");
            return 1024;
        });

        completableFuture.whenComplete((t,u)->{
            System.out.println("t->"+t);//正常的返回结果
            System.out.println("u->"+u);//错误信息：
        }).exceptionally((e)->{
           System.out.println( e.getMessage());//可以获取到错误的结果
           return 233;
        });


    }
}

```

# JMM

> volatile 理解

Volatile 的java虚拟机的**轻量级的同步机制**

1. 保证可见性
2. ==不保证原子性==
3. 禁止指令重排

> JMM what?

JMM:Java内存模型，不存在东西，概念！约定！

1. 线程解锁之前，必须把共享变量==立刻==刷回主存。
2. 线程加锁前，必须读取主存中的最新值到工作内存中！
3. 加锁和解锁是同一把锁

![1589560296796](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589560296796.png)

线程工作内存，主内存的八种操作:

![1589560443703](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589560443703.png)

`贮存不可见出现的问题。`

![1589560534884](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589560534884.png)

# Volatile

程序不知道主内存中的值已经被修改过了

![1589560879765](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589560879765.png)



> 保证可见性

```java
package com.chen.tvolatile;

import java.util.concurrent.TimeUnit;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/16 0:36
 * @Version V1.0
 **/

public class JMMDemo {
    //不加volatile程序就回死循环，因为程序不可见
    private volatile  static int num =0;
    public static void main(String[] args) {
        //主线程

        new Thread(()->{//线程1
            while (num == 0){

            }
        }).start();

        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        num =1;
        System.out.println(num);

    }
}

```

> 不保证原子性

```java
package com.chen.tvolatile;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/16 0:43
 * @Version V1.0
 **/

public class VDemo02 {

    //volatile 不保证原子性
    private volatile static int num = 0;

    public static void add(){
        num++;
    }

    public static void main(String[] args) {
        //理论上结果应该为2W
        for (int i = 0; i < 20; i++) {
            new Thread(()->{
                for (int j = 0; j < 1000; j++) {
                    add();
                }
            }).start();
        }

        while (Thread.activeCount()>2){
            Thread.yield();
        }
        System.out.println(Thread.currentThread().getName()+" "+num);
    }
}

```

> 指令重排

**Volatile可以避免指令重排：**

内存屏障，CPU指令。作用:

1. 保证特定的操作顺序
2. 可以保证某些变量的内存可见性(利用这些特性，就可以保持)

![1589562059722](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589562059722.png)

volatile 可以保证可见性，不能保证原子性,由于内存屏障，可以保证避免指令重排的现象产生!

# 单单单 单例模式

> 饿汉式

```java
package com.chen.Single;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/16 1:03
 * @Version V1.0
 **/

public class Hungry {

    //耗费内存，还没使用就已经开始加载了
    private Hungry(){

    }

    private  final static Hungry HUNGRY = new Hungry();

    public static Hungry getInstance(){
        return HUNGRY;
    }

}

```

> 懒汉式

```java
package com.chen.Single;

import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;
import java.util.TreeMap;
import java.util.function.Consumer;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/16 1:05
 * @Version V1.0
 **/
//懒汉式 单例
public class LazyMan {

    private static boolean flag = false;

    private LazyMan(){
      synchronized (LazyMan.class){
          if (flag == false){
              flag = true;
          }else {
              throw new RuntimeException("不要试图使用反射破坏异常");
          }
      }
    }

    private volatile static LazyMan lazyMan;

    //双重检测锁模式下 ，懒汉式单例模式 DCL懒汉式
    public static LazyMan getInstance(){
        if (lazyMan==null){
            synchronized (LazyMan.class){
                if (lazyMan==null){
                    lazyMan = new LazyMan();//不是一个原子性操作
                    /**
                     * 1.  分配内存空间
                     * 2.执行构造方法，初始化对象
                     * 3.把这个对象指向这个空间
                     */
                }
            }
        }
        return lazyMan;
    }

    // 单线程下是ok的
    public static void main(String[] args) throws NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
       LazyMan instance = LazyMan.getInstance();
        Constructor<LazyMan> declaredConstructor = LazyMan.class.getDeclaredConstructor();
        declaredConstructor.setAccessible(true);
        LazyMan instance2 = declaredConstructor.newInstance();

        System.out.println(instance);
        System.out.println(instance2);
    }

}

```

> 枚举实现

```java
package com.chen.Single;

import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/16 1:19
 * @Version V1.0
 **/
//枚举
public enum EnumSingle {

    INSTANCE;

    public EnumSingle getInstance(){
        return INSTANCE;
    }
}

class Test{
    public static void main(String[] args) throws NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
        EnumSingle instance1 = EnumSingle.INSTANCE;
        Constructor<EnumSingle> declaredConstructor = EnumSingle.class.getDeclaredConstructor(null);
        declaredConstructor.setAccessible(true);
        EnumSingle instance2 = declaredConstructor.newInstance();
        System.out.println(instance1);
        System.out.println(instance2);

    }
}

```

> 静态内部类

```java
package com.chen.Single;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/16 1:11
 * @Version V1.0
 **/
//静态内部类
public class Holder {
    private Holder(){

    }

    public static Holder getInstance(){
        return InnerClass.holder;
    }

    public static class InnerClass{
        private static final Holder holder = new Holder();
    }
}

```

# 理解CAS

> 什么是CAS

```java
package com.chen.cas;

import java.util.concurrent.atomic.AtomicInteger;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/16 1:29
 * @Version V1.0
 **/

public class CASDemo {

    AtomicInteger atomicInteger = new AtomicInteger(2020);

    //CAS  compareAndSet：比较并交换!
    public static void main(String[] args) {
        AtomicInteger atomicInteger = new AtomicInteger(2020);

        //如果我期望的值达到了就更新，否则就不更新  CAS 是CPU的并发原语！
        System.out.println(atomicInteger.compareAndSet(2020, 2021));
        System.out.println(atomicInteger.get());

        atomicInteger.getAndIncrement();

        System.out.println(atomicInteger.compareAndSet(2020, 2021));
        System.out.println(atomicInteger.get());

    }
}
```

CAS：比较当前工作内存中的值，如果这个值是期望的，那么则执行操作！如果不是就一直循环！

**缺点：**

1. 自旋锁循环耗时
2. 一次性只能保证一个共享变量的原子性
3. 存在ABD问题





> Unsafe类

![1589564169311](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589564169311.png)

![1589564279027](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589564279027.png)



> ABA 问题

![1589564605832](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589564605832.png)

```java
package com.chen.cas;

import java.util.concurrent.atomic.AtomicInteger;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/16 1:29
 * @Version V1.0
 **/

public class CASDemo {

    AtomicInteger atomicInteger = new AtomicInteger(2020);

    //CAS  compareAndSet：比较并交换!
    public static void main(String[] args) {
        AtomicInteger atomicInteger = new AtomicInteger(2020);

        //对于我们平时写的sql：乐观锁!
        //如果我期望的值达到了就更新，否则就不更新  CAS 是CPU的并发原语！
        System.out.println("========捣乱的线程=======================");
        System.out.println(atomicInteger.compareAndSet(2020, 2021));
        System.out.println(atomicInteger.get());

        System.out.println(atomicInteger.compareAndSet(2021, 2020));
        System.out.println(atomicInteger.get());

        System.out.println("===========================================");
        System.out.println(atomicInteger.compareAndSet(2020, 2021));
        System.out.println(atomicInteger.get());

    }
}

```

# 各种锁的理解

## 1.公平锁，非公平锁

公平锁:非常公平，不能够插队，必须先来后到

非公平锁：非常不公平，可以插队(默认都是非公平的)

```java
public ReentrantLock() {
        sync = new NonfairSync();
    }

public ReentrantLock(boolean fair) {
        sync = fair ? new FairSync() : new NonfairSync();
    }
```



## 2. 可重入锁

可重入锁(递归锁)

![1589565740304](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589565740304.png)

> Synchronized

```java
package com.chen.lock;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/16 2:02
 * @Version V1.0
 **/
//Synchronized
public class Demo01 {
    public static void main(String[] args) {
        Phone phone = new Phone();

        new Thread(()->{
            phone.ms();
        },"A").start();

        new Thread(()->{
            phone.ms();
        },"B").start();
    }
}

class Phone{
    public synchronized void ms(){
        System.out.println(Thread.currentThread().getName()+"sms");
        call();// 这里也有锁
    }

    public synchronized void call(){
        System.out.println(Thread.currentThread().getName()+"call");
    }
}
```

> Lock

```java
package com.chen.lock;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/16 2:06
 * @Version V1.0
 **/

import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/16 2:02
 * @Version V1.0
 **/
//Synchronized
public class Demo02 {
    public static void main(String[] args) {
        Phone2 phone = new Phone2();

        new Thread(()->{
            phone.ms();
        },"A").start();

        new Thread(()->{
            phone.ms();
        },"B").start();
    }
}

class Phone2{

    Lock lock = new ReentrantLock();

    public  void ms(){
        lock.lock();
        try{
            System.out.println(Thread.currentThread().getName()+"sms");
            call();// 这里也有锁
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }

    public  void call(){
        //锁必须配对
        lock.lock();
        try{
            System.out.println(Thread.currentThread().getName()+"call");
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
}
```

# 自旋锁

spinLock

![1589566348535](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589566348535.png)

```java
package com.chen.lock;

import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.ReentrantLock;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/16 2:15
 * @Version V1.0
 **/

public class TestSpinLock {
    public static void main(String[] args) throws InterruptedException {
        ReentrantLock reentrantLock = new ReentrantLock();
        reentrantLock.lock();
        reentrantLock.unlock();

        // 底层使用自旋锁实现的
        SpinlockDemo lock = new SpinlockDemo();

        new Thread(()->{
            lock.myLock();
            try{
                TimeUnit.SECONDS.sleep(3);
            }catch (Exception e){
                e.printStackTrace();
            }finally {
                lock.myUnLock();
            }
        },"T1").start();


        TimeUnit.SECONDS.sleep(1);

        new Thread(()->{
            lock.myLock();
            try{
                TimeUnit.SECONDS.sleep(1);
            }catch (Exception e){
                e.printStackTrace();
            }finally {
                lock.myUnLock();
            }
        },"T2").start();
    }
}
```

# 死锁

![1589566868827](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589566868827.png)

```java
package com.chen.lock;

import java.util.concurrent.TimeUnit;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/5/16 2:21
 * @Version V1.0
 **/

public class DeadLockDemo {
    public static void main(String[] args) {

        String lockA = "lockA";
        String lockB = "lockB";

        new Thread(new MyThread(lockA,lockB),"T1").start();
        new Thread(new MyThread(lockB,lockA),"T2").start();
    }
}

class MyThread implements Runnable{

    private String lockA;
    private String lockB;

    public MyThread(String lockA,String lockB){
        this.lockA = lockA ;
        this.lockB = lockB;
    }

    @Override
    public void run() {
        synchronized (lockA){
            System.out.println(Thread.currentThread().getName()+"lock："+lockA+"=>get"+lockB);

            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            synchronized (lockB){
                System.out.println(Thread.currentThread().getName()+"lock："+lockB+"=>get"+lockA);
            }
        }
    }

    private String
}
```

> 解决问题

1. 使用`jps`定位进程号

![1589567220487](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\1589567220487.png)

2. 使用`jstack进程号 `查看进行信息























