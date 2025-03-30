---
title: beanToMap
date: 2023-12-10 23:38:20
tags: [spring,hutool]
---

# 对象转map

<!--more-->

# 1.使用hutool工具类

```xml
 <dependency>
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
            <version>5.8.23</version>
        </dependency>
```



2.使用对象beantomap

```java
 */
public class BeanToMap {


    public static void main(String[] args) {
        User user = new User();
        user.setName("张三");
        user.setAddress("北京");
        Map<String, Object> map = BeanUtil.beanToMap(user);
        //bean转map
        System.out.println(map);
        // //获取对象的所有key
        String string = map.values().toString();
        System.out.println(string);
        //获取对象的所有value
        String key = map.keySet().toString();
        System.out.println(key);
    }
}
@Data
class User {

    public String name;


    public String address;

}

```

