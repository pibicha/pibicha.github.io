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
                    waitNotityTestClass.notify(); //  通知消费者以产出——等待消费 （fixme 这里我找了下为啥不会唤醒自己，网上看到的解释说是唤醒第一个相关线程；由于生产者和消费者是并发执行，消费者的“空腹等待”发生在生产者的“等待消费”之前吧…… 这里不是很严谨。。）
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