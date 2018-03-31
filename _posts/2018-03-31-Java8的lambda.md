---
title: 设计模式个人总结
date: 2018-03-31 10:13:00
categories:
- No Matter
tags:
- Java
---  


Java8的lambda用起来既舒服又恶心，不像python，Java8里面要用lambda得先确定一个
函数式接口（Function、Consumer等）；受限制于Java语法，每次想再Java中使用闭包
（lambda）必须得找到一个参数和返回值适合的函数接口；不过今天显得没事，看了看IDEA
的keymap发现有个Parameter Function的功能，猜想是不是将代码变成lambda参数的功能，
一试验还真是。。。 哈哈哈哈 mac 下面是shift option command + p； 简直爽

吐槽一下Java8的lambda
其实Java8以前就有lambda，只不过那会叫做匿名内部类而已，用起来巨丑，不优雅
，得new 一个接口，然后再'{}'内实现该接口的方法； Java8的lambda实现起来，实际
也是new 了一个接口；不过这个接口只能有一个抽象方法（不包括default修饰的）；
这种接口也叫函数式接口，可以被@Functional注解标记，被这个注解标记的接口只能有
一个抽象方法，类似于@Overwrite的功能吧，让程序元在编写时就能发现问题；
在其他语言中，使用lambda会出现“自由变量捕获”问题，JS和python中都常见，而在Java
中为了避免这种问题，直接将闭包中要使用到的变量强制要求为final类型了。。。。
哈哈哈哈， Java真是666