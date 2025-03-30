---
title: bean的注入方式
date: 2023-04-06 20:11:37
tags: spring
excerpt: Java Bean 属性注入方式
---

#### Java Bean 属性注入方式：

##### 1.Set 方法注入：

使用特定的 Setter 方法来对 Bean 中的属性值进行注入。

```java
public class UserBean {
    private String username;
    private String password;
    
    public UserBean() {}
    
    public void setUsername(String username) {
        this.username = username;
    }
    
    public void setPassword(String password) {
        this.password = password;
    }
    
    // getter 方法省略
    
}
```



在使用该 Bean 时，可以通过调用对应的 Setter 方法来注入属性值：

```java
UserBean user = new UserBean();
user.setUsername("admin");
user.setPassword("123456");
```



##### 2.构造方法注入

使用 Bean 的构造方法对属性值进行注入。

```
public class UserBean {
    private String username;
    private String password;
    
    public UserBean(String username, String password) {
        this.username = username;
        this.password = password;
    }
    
    // getter 方法省略
    
}
```



在使用该 Bean 时，可以通过调用带有参数的构造方法来注入属性值：

```java
UserBean user = new UserBean("admin", "123456");
```



##### 3.注解注入

1. 使用注解对 Bean 中的属性进行注入，常见的注解包括 @Autowired、@Inject 等。示例代码如下：

```
public class UserBean {
    @Autowired
    private UserService userService;
    
    // Setter、Getter 方法省略
    
}
```



2. 在使用该 Bean 时，可以使用 Spring 容器注入 UserService 的实例对象来自动对 UserBean 中的 userService 进行注入：

```java
@Component
public class UserService {
    // 相关方法和属性省略
}

@Component
public class UserController {
    @Autowired
    private UserService userService;
    
    // 相关方法省略
}
```
