---
title: 类加载机制&static理解 
date: 2018-03-30 20:13:00
categories:
- JVM
tags:
- [ClassFile,static]
---


**ClassFile与类加载机制的关系**  
“计算机科学中的任何难题都可以通过增加一个中间层来解决”。ClassFile就是为了实现类加载功能的中间层。

根据[jvm.go](https://github.com/zxh0/jvm.go)总结的一些ClassFile与类加载机制的关系  

### 加载  
加载过程，实际上就是搜索class文件的过程，项目中遇到的指定war、jar包，就是为了找到class文件；  
除了通过`java -cp|-classpath /path/to/classfile`指定class文件的路径以外，还可以通过网络和数据库，还可以通过反射指定类路径  

加载的时机，在遇到new、get/putstatic时会触发，也就是平时new 对象和调用静态方法时就会触发类的加载；

### 校验  
到达校验这一步，已经和class文件结构有关了；class文件的结构如下：  
```goland
ClassFile {
u4 magic; // 魔数 ‘CAFEBABE’
u2 minor_version; // 次版本号
u2 major_version; // 主版本号
u2 constant_pool_count; // 常量池计数;
cp_info constant_pool[constant_pool_count-1]; // 常量池
u2 access_flags; // 访问控制符
u2 this_class; // 本类的全类名
u2 super_class; // 父类
u2 interfaces_count; // 继承接口数
u2 interfaces[interfaces_count]; // 继承接口全类名
u2 fields_count; // 属性数
field_info fields[fields_count]; // 属性
u2 methods_count; // 方法数
method_info methods[methods_count]; // 方法表
u2 attributes_count;
attribute_info attributes[attributes_count]; 
}
```
校验阶段，会验证class文件的二进制流是否已“CAFEBABE”开头，所属的版本号是否可用；以及校验常量池中存放的字面量字符是否合法，常量池中的`CONSTANT_Utf8_info`常量是否合法；  
按class文件结构，会校验class文件的元信息比如；类的超类是否存在，implement的接口是否有实现其方法，  
在接下来是更深层的字节码验证，先跳过
### 准备  
准备这一步，和`static`关键字有关； 对于static的理解，直接把迁移博客之前的内容复制过来了：  
```text
面试时很多人问我对static的理解；
对他的定义 网上通常说是“内存中只存在一份”，“只依托与类、不能用对象调用”； 也有人说
static是java中的后门，类似于其他语言中的全局变量。
我个人对static的理解是，static是不在Java“五行”中的存在，static不依托于类，但是又受限制
于java的语法规则，使得它不得不书写在某一个类中； 于是便有“只依托与类、不能用对象调用”
的说法，其实static也不依托于类，只是它怎么也得写到某个类中， 既然它都不依托于类，那么
类实例想调用static的变量和方法也是不可能的（编译时和稍微智能点的工具都会报无法引用静态上下文的错）
所以，在类加载的校验和链接过程中，准备阶段即是为了类的静态属性准备初始空间的（如果被final标记了，
会直接初始化），又因为static高于类的存在，所以在链接之前，即会执行“准备”的步骤
```   

### 解析  
这一步是将class文件中的常量池中的字符引用转为直接引用的过程，为`初始化`做准备；在class文件常量池中的字面量终究只是字符的形式存在，与java内存没有关系，这一步会将其转为JVM内存中的地址引用；  

### 初始化  
以上的工作后完成后，类实例的初始化就水到渠成（实际上这里我没咋看）
初始化过程中，JVM会自动调用父类的clinit方法（如果父类有静态属性和静态代码块的话），也就是网上那些面试题忽悠B类继承A类，各有一些静态方法，考察调用顺序之类的

