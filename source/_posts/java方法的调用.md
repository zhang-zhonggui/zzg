---
title: java方法的调用
date: 2024-03-26 20:37:26
tags: java	
---

java方法调用值传递

<!--more-->

java在调用方法时传参如果是基础类型传入的是值如果是对象的形象传入的是地址,

但其中有个意外就是String如果是String的话传入的也是值其他的是地址

```java
public class Test {
    public static void main(String[] args) {
        HashMap<String, String> map = new HashMap<>(10);
        map.put("aa", "aa");
        String s = "123";
        objectTest(map);
        stringTest(s);
        System.out.println(s);
        System.out.println(map);
    }
    /**
     * 传入String 时传入的时值
     * @param s 字符串
     */
    static void stringTest(String s) {
        s = s + "abc";
        System.out.println(s);
    }
    /**
     * 对象的形象传入的是地址
     *
     * @param map 对象
     */
    static void objectTest(Map<String, String> map) {
        map.put("bb", "aa");
    }
}
```

![image-20240617165305293](https://cdn.jsdelivr.us/gh/zhang-zhonggui/blog/bolg/image-20240617165305293.png)

