---
title: rabbitmq消费者限流
date: 2024-08-18 19:54:33
tags: [Spring Boot,rabbitmq]
---

# rabbitmq结合Guava对消费者进行消费者限流

<!--more-->

## 1.配置RabbitMQ消息监听器容器工厂

```java

@Configuration
public class RabbitConfig {
    public static final String MESSAGE_QUEUE = "message_queue";

    /**
     * 配置RabbitMQ消息监听器容器工厂
     * 该工厂用于创建处理单个消息的监听器容器
     *
     * @param connectionFactory RabbitMQ连接工厂，用于建立与RabbitMQ的连接
     * @return SimpleRabbitListenerContainerFactory实例，配置用于手动确认消息的监听器容器
     */
    @Bean
    public SimpleRabbitListenerContainerFactory singleMessageListenerContainerFactory(ConnectionFactory connectionFactory) {
        SimpleRabbitListenerContainerFactory factory = new SimpleRabbitListenerContainerFactory();
        factory.setConnectionFactory(connectionFactory);
        factory.setAcknowledgeMode(AcknowledgeMode.MANUAL); // 设置手动确认模式，确保消息处理完成后才从队列中移除
        factory.setPrefetchCount(1); // 每次只拉取一条消息，确保消息不会在消费者中积压
        return factory;
    }
		
    @Bean
    public Queue sendMessageQueue() {
        return new Queue(MESSAGE_QUEUE, true);
    }
}

```

## 2.消费端使用guava的RateLimiter进行限流

```java
@Component
@Slf4j
public class SendMessageConsumer {
    // 限流器，每秒允许消费1个消息
    private final RateLimiter rateLimiter = RateLimiter.create(1);

    @RabbitListener(queues = RabbitConfig.MESSAGE_QUEUE, containerFactory = "singleMessageListenerContainerFactory")
    public void onMessage(Channel channel, String message, @Header(AmqpHeaders.DELIVERY_TAG) long deliveryTag) {
        try {
            if (rateLimiter.tryAcquire()) {
                // 处理消息
                log.info("消费成功 message: {}, 消费时间: {}", message, LocalDateTime.now());
                // 确认消息
                channel.basicAck(deliveryTag, false);
            } else {
                // 限流，拒绝消息并重新入队
                channel.basicNack(deliveryTag, false, true);
            }
        } catch (Exception e) {
            log.error("消息处理异常 message: {}, 错误信息: {}", message, e.getMessage(), e);
            try {
                // 处理失败，拒绝消息并重新入队
                channel.basicNack(deliveryTag, false, true);
            } catch (IOException ex) {
                log.error("消息拒绝失败 deliveryTag: {},  错误信息: {}", deliveryTag, ex.getMessage(), ex);
            }
        }
    }
}
```



## 3.效果展示

![](https://i.p-i.vip/24/20240818-66c1e273b9d46.png)

