---
title: 使用nacos出现的bug
date: 2023-04-20 22:09:49
tags: spring-cloud
categories: 学习
---

# nacos使用出现的问题

<!--more-->

# 起因

我想使用nacos写一个读取配置文件的，并修改配置文件的一个小demo。结果创建项目，启动的时候一直出现bug出现问题也不知道该怎么做，报错内容也一直是读取不到配置文件的信息，我一直以为是我那里写的有问题一直找不着配置文件。

```txt
Add a spring.config.import=nacos: property to your configuration.
	If configuration is not required add spring.config.import=optional:nacos: instead.
	To disable this check, set spring.cloud.nacos.config.import-check.enabled=false.
```



# 过程

在百度上面找了半个小时没有找到解决的办法直到将整个问题上传到bing搜索才发现问题的原因，原来**是2021.0.5版本的 Spring Cloud 默认不再启用 bootstrap 包**所以我之前启动一直报错，并不是由于找不到配置文件的原因

# 解决方案

## 方法一：将bootstrap.yaml改成application.yaml

## 方法二：添加jar包

新版本添加

```java
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bootstrap</artifactId>
</dependency>
```

旧版本及2.4以下

```java
    <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-context</artifactId>
    </dependency>
```

