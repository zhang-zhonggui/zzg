---
title: springboot整合kafka报错
date: 2024-05-31 21:22:01
tags: [Spring,kafka]
---



### springboot整合kafka报错

<!--more-->

#### 1.错误内容

```java
java.net.UnknownHostException: 不知道这样的主机。 (kafka)
	at java.base/java.net.Inet6AddressImpl.lookupAllHostAddr(Native Method) ~[na:na]
	at java.base/java.net.Inet6AddressImpl.lookupAllHostAddr(Inet6AddressImpl.java:52) ~[na:na]
	at java.base/java.net.InetAddress$PlatformResolver.lookupByName(InetAddress.java:1211) ~[na:na]
	at java.base/java.net.InetAddress.getAddressesFromNameService(InetAddress.java:1828) ~[na:na]
	at java.base/java.net.InetAddress$NameServiceAddresses.get(InetAddress.java:1139) ~[na:na]
	at java.base/java.net.InetAddress.getAllByName0(InetAddress.java:1818) ~[na:na]
	at java.base/java.net.InetAddress.getAllByName(InetAddress.java:1688) ~[na:na]
	at org.apache.kafka.clients.DefaultHostResolver.resolve(DefaultHostResolver.java:27) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.ClientUtils.resolve(ClientUtils.java:122) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.ClusterConnectionStates$NodeConnectionState.currentAddress(ClusterConnectionStates.java:510) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.ClusterConnectionStates$NodeConnectionState.access$200(ClusterConnectionStates.java:467) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.ClusterConnectionStates.currentAddress(ClusterConnectionStates.java:173) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.NetworkClient.initiateConnect(NetworkClient.java:1030) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.NetworkClient.ready(NetworkClient.java:301) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.consumer.internals.ConsumerNetworkClient.tryConnect(ConsumerNetworkClient.java:590) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.consumer.internals.AbstractCoordinator$FindCoordinatorResponseHandler.onSuccess(AbstractCoordinator.java:907) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.consumer.internals.AbstractCoordinator$FindCoordinatorResponseHandler.onSuccess(AbstractCoordinator.java:883) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.consumer.internals.RequestFuture$1.onSuccess(RequestFuture.java:206) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.consumer.internals.RequestFuture.fireSuccess(RequestFuture.java:169) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.consumer.internals.RequestFuture.complete(RequestFuture.java:129) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.consumer.internals.ConsumerNetworkClient$RequestFutureCompletionHandler.fireCompletion(ConsumerNetworkClient.java:617) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.consumer.internals.ConsumerNetworkClient.firePendingCompletedRequests(ConsumerNetworkClient.java:427) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.consumer.internals.ConsumerNetworkClient.poll(ConsumerNetworkClient.java:312) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.consumer.internals.ConsumerNetworkClient.poll(ConsumerNetworkClient.java:230) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.consumer.internals.AbstractCoordinator.ensureCoordinatorReady(AbstractCoordinator.java:265) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.consumer.internals.AbstractCoordinator.ensureCoordinatorReady(AbstractCoordinator.java:240) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.consumer.internals.ConsumerCoordinator.coordinatorUnknownAndUnreadySync(ConsumerCoordinator.java:504) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.consumer.internals.ConsumerCoordinator.poll(ConsumerCoordinator.java:536) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.consumer.KafkaConsumer.updateAssignmentMetadataIfNeeded(KafkaConsumer.java:1220) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.consumer.KafkaConsumer.poll(KafkaConsumer.java:1179) ~[kafka-clients-3.6.1.jar:na]
	at org.apache.kafka.clients.consumer.KafkaConsumer.poll(KafkaConsumer.java:1159) ~[kafka-clients-3.6.1.jar:na]
	at org.springframework.kafka.listener.KafkaMessageListenerContainer$ListenerConsumer.pollConsumer(KafkaMessageListenerContainer.java:1659) ~[spring-kafka-3.1.2.jar:3.1.2]
	at org.springframework.kafka.listener.KafkaMessageListenerContainer$ListenerConsumer.doPoll(KafkaMessageListenerContainer.java:1634) ~[spring-kafka-3.1.2.jar:3.1.2]
	at org.springframework.kafka.listener.KafkaMessageListenerContainer$ListenerConsumer.pollAndInvoke(KafkaMessageListenerContainer.java:1432) ~[spring-kafka-3.1.2.jar:3.1.2]
	at org.springframework.kafka.listener.KafkaMessageListenerContainer$ListenerConsumer.run(KafkaMessageListenerContainer.java:1323) ~[spring-kafka-3.1.2.jar:3.1.2]
	at java.base/java.util.concurrent.CompletableFuture$AsyncRun.run$$$capture(CompletableFuture.java:1804) ~[na:na]
	at java.base/java.util.concurrent.CompletableFuture$AsyncRun.run(CompletableFuture.java) ~[na:na]
	at java.base/java.lang.Thread.run(Thread.java:1583) ~[na:na]
```

#### 2.错误原因

java.net.UnknownHostException: 不知道这样的主机。 (kafka) 在docker中找不到主机名称

#### 3.解决方法

修改host添加kafka的地址

```txt
92.168.2.38  kafka
```

参考https://blog.csdn.net/SuperrWatermelon/article/details/132048322#:~:text=%E8%A7%A3%E5%86%B3%E6%96%B9%E5%BC%8F%EF%BC%9A,%E5%B0%86kafka%E7%9A%84%E4%B8%BB%E6%9C%BA%E5%90%8D%E5%92%8C%E6%9C%8D%E5%8A%A1%E6%89%80%E5%9C%A8%E7%9A%84ip%E5%9C%B0%E5%9D%80%E9%85%8D%E7%BD%AE%E5%88%B0C%3AWindowsSystem32driversetc%E7%9B%AE%E5%BD%95%E4%B8%8B%E7%9A%84hosts%E6%96%87%E4%BB%B6%E4%B8%AD%EF%BC%8C%E5%B9%B6%E9%87%8D%E5%90%AF%E6%9C%8D%E5%8A%A1