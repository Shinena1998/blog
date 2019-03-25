---
title: "对于DI的认识"
date: 2019-03-23T00:14:14+08:00
categories:
  - Java
  - SpringBoot
tags:
  - IoC
draft: false
---
**控制反转**（Inversion of Control，缩写为**IoC**），是[面向对象编程](https://baike.baidu.com/item/%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E7%BC%96%E7%A8%8B)中的一种设计原则，可以用来减低计算机代码之间的[耦合度](https://baike.baidu.com/item/%E8%80%A6%E5%90%88%E5%BA%A6)。其中最常见的方式叫做**依赖注入**（Dependency Injection，简称**DI**），还有一种方式叫“依赖查找”（Dependency Lookup）。通过控制反转，对象在被创建的时候，由一个调控系统内所有对象的外界实体，将其所依赖的对象的引用传递给它。也可以说，依赖被注入到对象中。
<a name="d41d8cd9"></a>
#### 
<a name="5fbfe4c6"></a>
#### IoC是什么
**Ioc—Inversion of Control，即“控制反转”，不是什么技术，而是一种设计思想。**在Java开发中，**Ioc意味着将你设计好的对象交给容器控制，而不是传统的在你的对象内部直接控制。**如何理解好Ioc呢？理解好Ioc的关键是要明确“谁控制谁，控制什么，为何是反转（有反转就应该有正转了），哪些方面反转了”，那我们来深入分析一下：<br />　　●**谁控制谁，控制什么：**传统Java SE程序设计，我们直接在对象内部通过new进行创建对象，是程序主动去创建依赖对象；而IoC是有专门一个容器来创建这些对象，即由Ioc容器来控制对 象的创建；**谁控制谁？当然是IoC 容器控制了对象；控制什么？那就是主要控制了外部资源获取（不只是对象包括比如文件等）。**<br />　　●**为何是反转，哪些方面反转了：**有反转就有正转，传统应用程序是由我们自己在对象中主动控制去直接获取依赖对象，也就是正转；而反转则是由容器来帮忙创建及注入依赖对象；为何是反转？**因为由容器帮我们查找及注入依赖对象，对象只是被动的接受依赖对象，所以是反转；哪些方面反转了？依赖对象的获取被反转了。**

<a name="14323ee2"></a>
#### 那这些依赖应该在哪里呢？[https://blog.csdn.net/u014199143/article/details/80692127](https://blog.csdn.net/u014199143/article/details/80692127)
**Spring容器**<br />Spring容器是Spring的核心，一切Spring bean都存储在Spring容器内，并由其通过IoC技术管理。Spring容器也就是一个bean工厂（BeanFactory）。应用中bean的实例化，获取，销毁等都是由这个bean工厂管理的。<br />Spring容器究竟是什么。。。<br />org.springframework.context.ApplicationContext接口用于完成容器的配置，初始化，管理bean。一个Spring容器就是某个实现了ApplicationContext接口的类的实例。也就是说，从代码层面，Spring容器其实就是一个ApplicationContext。

<a name="7e15d2db"></a>
#### 如何将这些依赖注入到容器
能放到容器内的实例，首先必须是个bean。

<a name="18417552"></a>
#### bean又是什么呢？
java是面向对象的，对象其实是一类事务的抽象，bean就是一个类的一个实例，将一个实例注入到Spring容器后，我们自己的对象不需要再new一个类，而是直接在Spring容器内获取这个bean，然后使用。

<a name="4b5c2fd7"></a>
#### 那怎么能让一个POJO成为bean
@Bean,@Component , @Repository , @ Controller , @Service , @Configration这些注解都是把你要实例化的对象转化成一个Bean，放在IoC容器中。

<a name="ef8734c0"></a>
#### 那怎么使用这些bean
@Autowired 注释，它可以对类成员变量、方法及构造函数进行标注，完成自动装配的工作。
```java
@Autowired
private InfoService infoService;//拿到Spring容器内的InfoService。
```

<a name="c9e75175"></a>
#### 最后实现DI
```java
@GetMapping(value = "/service/info")
public List<InfoMysql> searchInfo(@RequestParam("confirm") boolean confirm,
                                  @RequestParam("count") int count){
  //传统模式中对象使用service中的方法需要new一个实例
  return new InfoService().pageInfo(count,infoRepository);
  
  //DI模式中，将bean注入到对象中。
  return infoService.pageInfo(count,infoRepository);
}
```

