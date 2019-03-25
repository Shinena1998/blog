---
title: "对于DI的认识"
date: 2019-03-23T00:14:14+08:00
categories:
  - Java
  - SpringBoot
tags:
  - Bean
draft: false
---
<a name="8c4c7a94"></a>
### 第一类
<a name="a36a6d69"></a>
#### @Repository、@Service、@Controller 和@Component
> 这四个注解是在类上面使用。其作用都是将一个类转化为一个bean，并注入到Spring容器内，其不同点只是标记bean的不同功能而已。


> @Repository对应数据访问层Bean，@Service对应业务层Bean，@Controller对应控制层Bean，
> @Component泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注，不推荐使用。


> 这些注解会在项目启动时，默认执行类的构造函数进行实例化。


```java
@Service
public class InfoService {
    public InfoService(){
        System.out.println("InfoService被注入到了Spring容器内");
    }
}

```

<a name="3e8bda24"></a>
### 第二类
<a name="ace79a72"></a>
#### @Bean与@Configuration
> @Configuration用于定义配置类，可替换xml配置文件，被注解的类内部包含有一个或多个被@Bean注解的方法，这些方法将会被AnnotationConfigApplicationContext或AnnotationConfigWebApplicationContext类进行扫描，并用于构建bean定义，初始化Spring容器

> @Bean用在方法上，表示该方法返回一个bean。
> 

```java
public class Channel {
    private int threads;
    public Channel(int threads){
				this.threads = threads;
    }
}
@Configuration
public class WorkerConfigruation {
    @Bean
    public Channel setWorker(){
        System.out.println("Channel被注入到Spring容器内");
        return new Channel(2);
    }
}
```


<a name="d9bc9e1c"></a>
#### 两种测试结果

```java
//在项目启动时成功注入了容器内
2019-03-23 00:08:11.830 logbak-laf [main] INFO  o.h.h.i.QueryTranslatorFactoryInitiator - HHH000397: Using ASTQueryTranslatorFactory
2019-03-23 00:08:12.001 logbak-laf [main] INFO  o.s.o.j.LocalContainerEntityManagerFactoryBean - Initialized JPA EntityManagerFactory for persistence unit 'default'
InfoService被注入到了Spring容器内
Channel被注入到Spring容器内
2019-03-23 00:08:14.818 logbak-laf [main] INFO  o.s.w.s.h.SimpleUrlHandlerMapping - Mapped URL path [/**/favicon.ico] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
```

<a name="b127276b"></a>
#### 那什么时候用第一类什么时候用第二类？

前面说过，第一类是默认执行类的构造方法，那如果重写有参构造方法，第一类会在项目启动时报错，找不到int数据，应该用第二类。
