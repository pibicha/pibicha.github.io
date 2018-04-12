---
title: wait、notify——生产者消费者
date: 2018-04-05 14:58:00
categories:
- Thread
tags:
- [Java,Thread]
---  

在Java中，wait、notify、notifyAll是Object的方法，在java中任何对象都可以作为锁，而这几个方法必须得在获取锁以后才能使用，否则会报`InterruptedException`异常  

在生产者和消费者模式中，假设：生产者每生产一个（也可以是诺干个，一个简单点）产品，就会等待他人消费以免浪费生产力；  
而消费者则是会等待生产者通知自己产品已产出，然后自己便去消费，随后通知生产者缺货了赶紧生产；这里比较简单的方式就是用java对象的wait和notify实现：  
```java
package com.git.poan;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * Created by poan on 2018/04/12.
 */
public class WaitNotityTest {


    public static void bake() {

        synchronized (WaitNotityTest.class) {
            for (int i = 1; i <= 10; i++) {

                Class<WaitNotityTest> waitNotityTestClass = WaitNotityTest.class;
                try {
                    Thread.sleep(3000);
                    System.out.println("烤好第" + i + "个面包");
                    waitNotityTestClass.wait(); // 每生产一个面包，即等待消费者去消费
                    waitNotityTestClass.notify(); //  通知消费者以产出——等待消费 （fixme 这里我找了下为啥不会唤醒自己，因为在上一行中，此线程已经放弃cpu使用权，没机会执行到这一步了；所以不会自己wait又调用notify唤醒自己的可能性！）
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    public static void eat() {
        synchronized (WaitNotityTest.class) {
            for (int i = 1; i <= 10; i++) {
                try {
                    Thread.sleep(3000);
                    System.out.println("吃了第" + i + "个面包");
                    Class<WaitNotityTest> waitNotityTestClass = WaitNotityTest.class;
                    waitNotityTestClass.notify(); // 通知生产者已消费；
                    waitNotityTestClass.wait(); // 等待生产者再次产出——空腹等待
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }


    public static void main(String[] args) throws InterruptedException {

        ExecutorService baker = Executors.newFixedThreadPool(3);
        ExecutorService eater = Executors.newFixedThreadPool(3);


        baker.execute(WaitNotityTest::bake);
        eater.execute(WaitNotityTest::eat);
    }

}

```

---  
在bake方法生产时，设定每生产一个产品都会进入等待，所以下一行代码就执行不到了，于是不可能会出现自己wait又自己notify的可能； 只有当拥有同一个锁的消费者在eat方法中，消费完以后通过notify方法，才能唤醒刚刚停滞在等待状态的生产者。  

