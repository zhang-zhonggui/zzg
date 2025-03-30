---
title: springboot线程池
date: 2024-04-05 14:26:14
tags: [Spring Boot]
---

Spring Boot线程池配置使用

<!--more-->

#### 1.在yaml中配置

```java
spring:
  task:
    execution:
      pool:
        # 核心线程数
        core-size: 8
        # 最大线程数
        max-size: 20
        # 线程空闲时间
        queue-capacity: 2000
        # 线程名
      thread-name-prefix: cache
```

##### 2.1在Spring Boot中使用

```java
    public void redisTest() {
        threadPoolTaskExecutor.execute(() -> {
                    log.info("{}线程执行", Thread.currentThread().getName());
                }
        );
        log.info("任务执行");
    }
```

##### 3.执行结果

![image-20240405143112537](https://cdn.jsdelivr.net/gh/zhang-zhonggui/blog/image-20240405143112537.png)

#### 2.使用注解使用

##### 2.1启动类注解添加@EnableAsync

```java
@SpringBootApplication
@EnableCaching
@MapperScan("com.example.cache.mapper")
@EnableAsync
public class CacheApplication {
    public static void main(String[] args) {
        SpringApplication application = new SpringApplication(CacheApplication.class);
        application.setApplicationStartup(new BufferingApplicationStartup(2048));
        application.run(args);
    }
}
```

##### 2.2在方法中添加注解@Async

```java
    @Async
    public void poolTest() {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
        log.info("异步执行");
    }
```

##### 2.3方法调用

```java

    @Resource
    private TestServiceImpl  testService;
    public void ThreadPoolTest() {
        testService.poolTest();
        log.info("任务执行");
    }
```

##### 2.4执行结果
![](https://cdn.jsdelivr.net/gh/zhang-zhonggui/blog/image-20240405144229229.png)
