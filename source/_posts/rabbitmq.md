---
title: rabbitmq
date: 2023-10-19 11:05:04
tags: [rabbitmq,Spring Boot]
---

# spring boot使用rabbitmq

<!--more-->

## 1、导包

```xml
      <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-amqp</artifactId>
        </dependency>
```

## 2 、编写配置文件

```java
spring:
  rabbitmq:
    port: 5672
    username: ribzdefp
    password: 3RSfYkVr102z7cJmkB2e-_pL-isCwOT4
    addresses: amqps://ribzdefp:3RSfYkVr102z7cJmkB2e-_pL-isCwOT4@gerbil.rmq.cloudamqp.com/ribzdefp
```

## 3、发送与接接受消息

#### 3.1、发送hello world 消息

##### 3.1.1、编写生产者

```java
@RestController
@RequestMapping("/hello")
@Tag(name = "发送hello word消息")
public class HelloWord {
    public static final String SUCCESS = "发送成功";
    
    @Resource
    private RabbitTemplate rabbitTemplate;

    @GetMapping("hello")
    @Operation(description = "发送hello word消息", summary = "发送hello word消息")
    public String send() {
        rabbitTemplate.convertAndSend(null, "hello", "zzg");
        return SUCCESS;
    }
}
```

##### 3.1.2、编写消费者

```java
@Configuration
public class Rabbitmq {

    public static final String QUEUE_NAME = "hello";

    @Bean
    public Queue queue() {
        return new Queue(QUEUE_NAME, false);
    }
}
```

```java
@Service
public class Hello {
    //该注解用于配置监听器监听队列“hello”
    @RabbitListener(queues = "hello")
    /**
     * 该方法用于将消息打印到控制台
     */
    public void msg(String msg) {
        System.out.println(msg);
    }
}
```



####  



