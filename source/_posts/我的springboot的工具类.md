---
title: 我的springboot的工具类
date: 2024-08-24 23:28:19
tags: Spring Boot
---

#  Spring Boot 工具类

<!--more-->

## RedisConfig的RedisTemplate序列化

```java
 /**
     * 配置RedisTemplate，用于定义Redis的数据序列化和反序列化方式
     * 对于Redis的操作，需要通过RedisTemplate来定义如何序列化和反序列化数据，
     * 这对Redis中的数据存储和读取至关重要
     *
     * @param connectionFactory Redis连接工厂，用于建立与Redis服务器的连接
     * @return 配置好的RedisTemplate实例，用于执行Redis操作
     */
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory connectionFactory) {
        // 创建RedisTemplate实例
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        // 设置连接工厂，使RedisTemplate能够与Redis服务器通信
        template.setConnectionFactory(connectionFactory);
        // 创建ObjectMapper实例，用于JSON数据的序列化和反序列化
        ObjectMapper mapper = new ObjectMapper();
        // 注册JavaTimeModule，以支持Java时间类型的数据序列化和反序列化
        mapper.registerModule(new JavaTimeModule());
        // 创建一个基于ObjectMapper的Redis数据序列化器
        GenericJackson2JsonRedisSerializer serializer = new GenericJackson2JsonRedisSerializer(mapper);
        // 设置RedisTemplate的键和值的序列化方式
        // 使用字符串序列化器对键进行序列化，使用自定义的JSON序列化器对值进行序列化
        template.setKeySerializer(RedisSerializer.string());
        template.setValueSerializer(serializer);
        // 设置RedisTemplate的哈希键和哈希值的序列化方式
        // 同样使用字符串序列化器对哈希键进行序列化，使用自定义的JSON序列化器对哈希值进行序列化
        template.setHashKeySerializer(RedisSerializer.string());
        template.setHashValueSerializer(serializer);
        // 调用afterPropertiesSet方法，完成RedisTemplate的初始化工作
        template.afterPropertiesSet();
        // 返回配置好的RedisTemplate实例
        return template;
    }
```

## Jackson序列化工具类

```java
package com.zzg.hospital.util;

import com.fasterxml.jackson.annotation.JsonInclude;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.core.type.TypeReference;
import com.fasterxml.jackson.databind.DeserializationFeature;
import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.SerializationFeature;
import com.fasterxml.jackson.datatype.jsr310.JavaTimeModule;
import com.google.common.base.Strings;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.io.IOException;
import java.text.SimpleDateFormat;
import java.util.List;
import java.util.Map;

/**
 * Jackson 工具类
 */
public class JacksonUtils {

    private static final Logger LOG = LoggerFactory.getLogger(JacksonUtils.class);

    private static final ObjectMapper OBJECT_MAPPER = createObjectMapper();

    private static ObjectMapper createObjectMapper() {
        ObjectMapper objectMapper = new ObjectMapper();
        // 序列化时忽略值为 null 的字段
        objectMapper.setSerializationInclusion(JsonInclude.Include.NON_NULL);
        // 序列化时不使用驼峰命名
        objectMapper.enable(SerializationFeature.INDENT_OUTPUT);
        objectMapper.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS);
        // 反序列化时忽略未知属性
        objectMapper.disable(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES);
        // 注册 Java 8 时间模块
        objectMapper.registerModule(new JavaTimeModule());
        // 设置日期格式
        SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        objectMapper.setDateFormat(dateFormat);
        return objectMapper;
    }

    /**
     * 对象转 JSON 字符串
     *
     * @param obj 对象
     * @return JSON 字符串
     */
    public static String toJson(Object obj) {
        if (obj == null) {
            return null;
        }
        try {
            return OBJECT_MAPPER.writeValueAsString(obj);
        } catch (JsonProcessingException e) {
            LOG.error("Error converting object to JSON: {}", obj, e);
            return null;
        }
    }

    /**
     * JSON 字符串转对象
     *
     * @param json  JSON 字符串
     * @param clazz 对象类型
     * @param <T>   对象类型
     * @return 对象
     */
    public static <T> T fromJson(String json, Class<T> clazz) {
        if (Strings.isNullOrEmpty(json)) {
            return null;
        }
        try {
            return OBJECT_MAPPER.readValue(json, clazz);
        } catch (IOException e) {
            LOG.error("Error converting JSON to object: {}", json, e);
            return null;
        }
    }

    /**
     * JSON 字符串转对象
     *
     * @param json          JSON 字符串
     * @param typeReference 类型引用
     * @param <T>           对象类型
     * @return 对象
     */
    public static <T> T fromJson(String json, TypeReference<T> typeReference) {
        if (Strings.isNullOrEmpty(json)) {
            return null;
        }
        try {
            return OBJECT_MAPPER.readValue(json, typeReference);
        } catch (IOException e) {
            LOG.error("Error converting JSON to object: {}", json, e);
            return null;
        }
    }

    /**
     * JSON 字符串转 Map
     *
     * @param json JSON 字符串
     * @return Map
     */
    public static Map<String, Object> fromJsonToMap(String json) {
        if (Strings.isNullOrEmpty(json)) {
            return null;
        }
        try {
            return OBJECT_MAPPER.readValue(json, new TypeReference<Map<String, Object>>() {});
        } catch (IOException e) {
            LOG.error("Error converting JSON to Map: {}", json, e);
            return null;
        }
    }

    /**
     * JSON 字符串转 List
     *
     * @param json JSON 字符串
     * @return List
     */
    public static List<Map<String, Object>> fromJsonToList(String json) {
        if (Strings.isNullOrEmpty(json)) {
            return null;
        }
        try {
            return OBJECT_MAPPER.readValue(json, new TypeReference<List<Map<String, Object>>>() {});
        } catch (IOException e) {
            LOG.error("Error converting JSON to List: {}", json, e);
            return null;
        }
    }

    /**
     * JSON 字符串转 JsonNode
     *
     * @param json JSON 字符串
     * @return JsonNode
     */
    public static JsonNode fromJsonToNode(String json) {
        if (Strings.isNullOrEmpty(json)) {
            return null;
        }
        try {
            return OBJECT_MAPPER.readTree(json);
        } catch (IOException e) {
            LOG.error("Error converting JSON to JsonNode: {}", json, e);
            return null;
        }
    }

    /**
     * 获取 JSON 字符串中的某个属性值
     *
     * @param json  JSON 字符串
     * @param field 属性名
     * @return 属性值
     */
    public static String getFieldValue(String json, String field) {
        if (Strings.isNullOrEmpty(json) || Strings.isNullOrEmpty(field)) {
            return null;
        }
        try {
            JsonNode node = OBJECT_MAPPER.readTree(json);
            return node.path(field).asText();
        } catch (IOException e) {
            LOG.error("Error getting field value from JSON: {}", json, e);
            return null;
        }
    }
}
```

Gson通用序列化工具类

```java
package com.zzg.hospital.util;

import com.google.gson.*;
import com.google.gson.reflect.TypeToken;

import java.lang.reflect.Type;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.List;
import java.util.Map;

public class GsonUtil {

    private static final String DATE_TIME_FORMAT = "yyyy-MM-dd HH:mm:ss";
    private static final Gson gson = new GsonBuilder()
            .registerTypeAdapter(LocalDateTime.class, new LocalDateTimeAdapter())
            .create();

    /**
     * 将 Java 对象转换为 JSON 字符串
     *
     * @param obj Java 对象
     * @return JSON 字符串
     */
    public static String toJson(Object obj) {
        return gson.toJson(obj);
    }

    /**
     * 将 JSON 字符串转换为 Java 对象
     *
     * @param json     JSON 字符串
     * @param classOfT Java 对象类型
     * @param <T>      Java 对象类型
     * @return Java 对象
     */
    public static <T> T fromJson(String json, Class<T> classOfT) {
        return gson.fromJson(json, classOfT);
    }

    /**
     * 将 JSON 字符串转换为 Java List 对象
     *
     * @param json     JSON 字符串
     * @param typeOfT Java List 对象类型
     * @param <T>      Java List 对象类型
     * @return Java List 对象
     */
    public static <T> List<T> fromJsonToList(String json, Type typeOfT) {
        return gson.fromJson(json, typeOfT);
    }

    /**
     * 将 JSON 字符串转换为 Java Map 对象
     *
     * @param json     JSON 字符串
     * @param keyType   Java Map 的 key 类型
     * @param valueType Java Map 的 value 类型
     * @param <K>      Java Map 的 key 类型
     * @param <V>      Java Map 的 value 类型
     * @return Java Map 对象
     */
    public static <K, V> Map<K, V> fromJsonToMap(String json, Class<K> keyType, Class<V> valueType) {
        Type type = new TypeToken<Map<K, V>>() {
        }.getType();
        return gson.fromJson(json, type);
    }

    /**
     * 格式化 JSON 字符串
     *
     * @param json JSON 字符串
     * @return 格式化后的 JSON 字符串
     */
    public static String formatJson(String json) {
        JsonElement je = JsonParser.parseString(json);
        return gson.toJson(je);
    }

    /**
     * LocalDateTime 适配器
     */
    private static class LocalDateTimeAdapter implements JsonSerializer<LocalDateTime>, JsonDeserializer<LocalDateTime> {
        private final DateTimeFormatter formatter = DateTimeFormatter.ofPattern(DATE_TIME_FORMAT);

        @Override
        public JsonElement serialize(LocalDateTime src, Type typeOfSrc, JsonSerializationContext context) {
            return new JsonPrimitive(formatter.format(src));
        }

        @Override
        public LocalDateTime deserialize(JsonElement json, Type typeOfT, JsonDeserializationContext context) throws JsonParseException {
            return LocalDateTime.parse(json.getAsString(), formatter);
        }
    }
}

```



