---
title: redis做mybatis的二级缓存
date: 2023-08-16 19:48:48
tags: [redis,Spring Boot,mybatis,redis]
---

##  redis做mybatis的二级缓存

<!-- more -->

项目版本： springboot：3.1.4 、数据库：MySQL 8.0.27、redis： 7.0.14、jkd17



### 1.导入相关jar包

```xml
        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
        </dependency>
        <dependency>
            <groupId>org.yaml</groupId>
            <artifactId>snakeyaml</artifactId>
            <version>2.1</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-cache</artifactId>
        </dependency>
        <dependency>
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
            <version>5.8.21</version>
        </dependency>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.13.0</version>
        </dependency>
        <!-- 使用 Redis 作为缓存实现，需要添加此 starter -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>com.google.guava</groupId>
            <artifactId>guava</artifactId>
            <version>32.1.2-jre</version>
        </dependency>
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.5.3.2</version>
        </dependency>
        <dependency>
            <groupId>com.github.xiaoymin</groupId>
            <artifactId>knife4j-openapi3-jakarta-spring-boot-starter</artifactId>
            <version>4.3.0</version>
        </dependency>
        <dependency>
            <groupId>com.mysql</groupId>
            <artifactId>mysql-connector-j</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
```



### 2.设置配置文件

```yaml
spring:
  jackson:
    date-format: yyyy-MM-dd HH:mm:ss
    time-zone: GMT+8
  datasource:
    # 数据库连接地址
    url: jdbc:127.0.0.1:3306/cache?useUnicode=true
    # 数据库用户名
    username: root
    # 数据库密码
    password: 123456
    # 数据库驱动类
    driver-class-name: com.mysql.cj.jdbc.Driver
    # 数据源类型
    type: com.zaxxer.hikari.HikariDataSource
  data:
    # redis连接地址
    redis:
      host: 127.0.0.1
      port: 11524
      lettuce:
        pool:
          max-active: 8
          max-idle: 8
          max-wait: -1
  cache:
    #缓存类型
    type: redis
    # redis缓存配置
    redis:
      # 缓存key前缀
      key-prefix: cache-
      #      # 是否缓存空值
      #      cache-null-values: true
      #过期时间15分钟
      time-to-live: 900s
mybatis-plus:
  type-aliases-package: com.example.cache.domain
  mapper-locations: classpath:mapper/*.xml
  configuration:
    cache-enabled: true
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
# springdoc-openapi项目配置
springdoc:
  swagger-ui:
    path: /swagger-ui.html
    tags-sorter: alpha
    operations-sorter: alpha
  api-docs:
    path: /v3/api-docs
  group-configs:
    - group: 'default'
      paths-to-match: '/**'
      packages-to-scan: com.example.cache.controller
# knife4j的增强配置，不需要增强可以不配
knife4j:
  enable: true
  setting:
    language: zh_cn
```

### 3.Mapper查询

```java
@Mapper
public interface UserMapper extends BaseMapper<User> {

    List<User> list();
}

```

### 4.Redis使用json序列化RedisTemplate

```java
   /**
     * redis序列化
     */
    @Bean(name = "RedisTemplate")
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<Object, Object> redisTemplate = new RedisTemplate<>();
        redisTemplate.setConnectionFactory(redisConnectionFactory);
        //设置key的序列化方式
        redisTemplate.setKeySerializer(RedisSerializer.string());
        //设置value的序列化方式
        redisTemplate.setValueSerializer(RedisSerializer.json());
        //设置hash的key的序列化方式
        redisTemplate.setHashKeySerializer(RedisSerializer.string());
        //设置hash的value的序列化方式
        redisTemplate.setHashValueSerializer(RedisSerializer.json());
        redisTemplate.afterPropertiesSet();
        return redisTemplate;
    }
```

### 5.实现自定义cache

```java
/**
 * @author 25212
 */
@Slf4j
public class MybatisRedisCache implements Cache {

    private final ReadWriteLock readWriteLock = new ReentrantReadWriteLock(true);


    private final RedisTemplate<String, Object> redisTemplate = SpringUtil.getBean("RedisTemplate");


    private final String id;

    public MybatisRedisCache(final String id) {
        if (StringUtils.isBlank(id)) {
            throw new IllegalArgumentException("Cache instances require an ID");
        }
        this.id = id;
    }


    @Override
    public String getId() {
        return this.id;
    }

    @Override
    public void putObject(Object key, Object value) {
        if (value != null) {
            log.debug("保存缓存");
            //保存数据并设置其过期时间为1天
            redisTemplate.opsForValue().set(key.toString(), value, Duration.ofDays(1));
        }
    }

    @Override
    public Object getObject(Object key) {
        try {
            if (key != null) {
                return redisTemplate.opsForValue().get(key.toString());
            }
        } catch (Exception e) {
            log.error("缓存出错 ");
        }
        return null;
    }

    @Override
    public Object removeObject(Object key) {
        if (key != null) {
            log.debug("清除缓存");
            redisTemplate.delete(key.toString());
        }
        return null;
    }

    @Override
    public void clear() {
        log.debug("清空缓存");
        Set<String> keys = redisTemplate.keys("*:" + this.id + "*");
        if (!CollectionUtils.isEmpty(keys)) {
            redisTemplate.delete(keys);
        }
    }

    @Override
    public int getSize() {
        Long size = redisTemplate.execute(RedisServerCommands::dbSize);
        return size != null ? size.intValue() : 0;
    }

    @Override
    public ReadWriteLock getReadWriteLock() {
        return this.readWriteLock;
    }
}

```

### 6.在mapper中开启二级缓存

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.example.cache.mapper.UserMapper">
<!--    指定自定义序列号方式-->
    <cache type="com.example.cache.cache.MybatisRedisCache"/>

    <select id="list" resultType="com.example.cache.domain.User">
        select  * from user
    </select>
</mapper>
```

