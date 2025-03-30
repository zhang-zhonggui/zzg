---
layout: spring
title: boot动态读取nacos
date: 2024-08-21 23:37:24
tags: [Spring Boot，Nacos]
---

# Spring Boot动态读取nacos配置文件

<!--more-->

## 1、默认当前已经可以读取配置nacos配置文件修改成动态读取在获取参数类上添加@RefreshScope注解即可

```java
@RequestMapping("/test")
@RestController
@RefreshScope
public class TestController {

    @Value("${zzg.zzg}")
    private String zzg;

    @GetMapping("/get")
    @ResponseBody
    public String get() {
        return zzg;
    }
}
```

```yaml
zzg:
  zzg: zzg
```

![image-20240822152629365](https://gcore.jsdelivr.net/gh/zhang-zhonggui/blog/20240825-66cab834321cf.png)

