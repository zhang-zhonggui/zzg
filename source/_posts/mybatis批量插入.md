---
title: mybatis批量插入
date: 2023-05-09 16:04:35
tags: MySQL
---

# mybatis批量插入

<!--more-->

## 1.使用Mybatis对MySQL的批量插入数据

1. 准备工作创建数据库

```sql
CREATE TABLE `students` (
  `id` int NOT NULL AUTO_INCREMENT,
  `name` varchar(50) NOT NULL,
  `age` int NOT NULL,
  `gender` enum('男性','女性') CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL,
  `address` varchar(255) NOT NULL,
  `phone` varchar(20) NOT NULL,
  `email` varchar(50) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
```

2. maven导入jar

   ```xml
      <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-test</artifactId>
               <scope>test</scope>
           </dependency>
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
           </dependency>
           <dependency>
               <groupId>com.baomidou</groupId>
               <artifactId>mybatis-plus-boot-starter</artifactId>
               <version>3.5.3.1</version>
           </dependency>
   ```

3.配置文件

```yaml
spring:
  datasource:
    url: jdbc:mysql://data.com:3306/data
    username: root
    password: root
    driver-class-name: com.mysql.cj.jdbc.Driver
```

## 2.对MySQL经行数据插入

### 2.1 一条一条的插入

1.Service

```java
@Service
public class UserService {
 
    @Resource
    private UserMapper userMapper;
 
    public void InsertUsers(){
        long start = System.currentTimeMillis();
        for(int i = 0 ;i < 10000; i++) {
            User user = new User();
            user.setUsername("name" + i);
            user.setPassword("password" + i);
            userMapper.insertUsers(user);
        }
        long end = System.currentTimeMillis();
        System.out.println(end-start + "ms" );
    }

}
```

2.mapper

```java
@Mapper
public interface UserMapper {
    Integer insertUsers(User user);
}
```

3.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.demo.mapper.StudentsMapper">
  <resultMap id="BaseResultMap" type="com.example.demo.domain.Students">
    <!--@mbg.generated-->
    <!--@Table students-->
    <id column="id" jdbcType="INTEGER" property="id" />
    <result column="name" jdbcType="VARCHAR" property="name" />
    <result column="age" jdbcType="INTEGER" property="age" />
    <result column="gender" jdbcType="OTHER" property="gender" />
    <result column="address" jdbcType="VARCHAR" property="address" />
    <result column="phone" jdbcType="VARCHAR" property="phone" />
    <result column="email" jdbcType="VARCHAR" property="email" />
  </resultMap>
  <sql id="Base_Column_List">
    <!--@mbg.generated-->
    id, `name`, age, gender, address, phone, email
  </sql>

  <insert id="bigSave">
      INSERT INTO students(id, name, age, gender, address, phone, email) VALUES
          (null, #{student.name}, #{student.age}, #{student.gender}, #{student.address}, #{student.phone},
           #{student.email})
  </insert>
</mapper>
```

4.消耗时间

 一万条数据总耗时：26348ms

### 2.2 MyBatis的手动批量提交

1. service

   ```java
   @Service
   public class UserService {
    
       @Resource
       private UserMapper userMapper;
    
       @Resource
       private SqlSessionTemplate sqlSessionTemplate;
    
       public void InsertUsers(){
           //关闭自动提交
           SqlSession sqlSession = sqlSessionTemplate.getSqlSessionFactory().openSession(ExecutorType.BATCH, false);
           UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
           long start = System.currentTimeMillis();
           for(int i = 0 ;i < 10000; i++) {
               User user = new User();
               user.setUsername("name" + i);
               user.setPassword("password" + i);
               userMapper.insertUsers(user);
           }
           sqlSession.commit();
           long end = System.currentTimeMillis();
           System.out.println("一万条数据总耗时：" + (end-start) + "ms" );
       }
   }
   ```

   2.消耗时间

   **一万条数据总耗时：24516ms**

### 2.3 MyBatis以集合方式批量新增

1.service

```java
@Service
public class UserService {
 
    @Resource
    private UserMapper userMapper;
 
    public void InsertUsers(){
        long start = System.currentTimeMillis();
        List<User> userList = new ArrayList<>();
        User user;
        for(int i = 0 ;i < 10000; i++) {
            user = new User();
            user.setUsername("name" + i);
            user.setPassword("password" + i);
            userList.add(user);
        }
        userMapper.insertUsers(userList);
        long end = System.currentTimeMillis();
        System.out.println("一万条数据总耗时：" + (end-start) + "ms" );
    }
 
}
```

2.mapper

```java
@Mapper
public interface UserMapper {
 
    Integer insertUsers(List<User> userList);
}
```

3.编写xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ithuang.demo.mapper.UserMapper">
    <insert id="insertUsers">
        INSERT INTO user (username, password)
        VALUES
        <foreach collection ="userList" item="user" separator =",">
            (#{user.username}, #{user.password})
        </foreach>
    </insert>
</mapper>
```

4、消耗时间

**一万条数据总耗时：521ms**

### 2.4 MyBatis-Plus提供的SaveBatch方法

1.service

```java
@Service
public class UserService extends ServiceImpl<UserMapper, User> implements IService<User> {
 
    public void InsertUsers(){
        long start = System.currentTimeMillis();
        List<User> userList = new ArrayList<>();
        User user;
        for(int i = 0 ;i < 10000; i++) {
            user = new User();
            user.setUsername("name" + i);
            user.setPassword("password" + i);
            userList.add(user);
        }
        saveBatch(userList);
        long end = System.currentTimeMillis();
        System.out.println("一万条数据总耗时：" + (end-start) + "ms" );
    }
}
```

2.mapper

```java
@Mapper
public interface UserMapper extends BaseMapper<User> {
 
}
```

3.输出结果4674ms

### 2.5 MyBatis-Plus提供的InsertBatchSomeColumn方法

1、编写EasySqlInjector 自定义类

```java

public class EasySqlInjector extends DefaultSqlInjector {
    @Override
    public List<AbstractMethod> getMethodList(Class<?> mapperClass, TableInfo tableInfo) {
        // 注意：此SQL注入器继承了DefaultSqlInjector(默认注入器)，调用了DefaultSqlInjector的getMethodList方法，保留了mybatis-plus的自带方法
        List<AbstractMethod> methodList = super.getMethodList(mapperClass, tableInfo);
        methodList.add(new InsertBatchSomeColumn(i -> i.getFieldFill() != FieldFill.UPDATE));
        return methodList;
    }
 
}
```

2.定义核心配置类注入此Bean

```java
@Configuration
public class MybatisPlusConfig {
 
    @Bean
    public EasySqlInjector sqlInjector() {
        return new EasySqlInjector();
    }
}
```

3、service

```java

public class UserService{
 
    @Resource
    private UserMapper userMapper;
    public void InsertUsers(){
        long start = System.currentTimeMillis();
        List<User> userList = new ArrayList<>();
        User user;
        for(int i = 0 ;i < 10000; i++) {
            user = new User();
            user.setUsername("name" + i);
            user.setPassword("password" + i);
            userList.add(user);
        }
        userMapper.insertBatchSomeColumn(userList);
        long end = System.currentTimeMillis();
        System.out.println("一万条数据总耗时：" + (end-start) + "ms" );
    }
}
```

4.输出结果

**一万条数据总耗时：575ms**

## 3.数据对比

|                    方法                     |  时间   |  推荐  |      |
| :-----------------------------------------: | :-----: | :----: | :--: |
|               一条一条的插入                | 26348ms | 不推荐 |      |
|            MyBatis的手动批量提交            | 24516ms | 不推荐 |      |
|          MyBatis以集合方式批量新增          |  521ms  |  推荐  |      |
|       MyBatis-Plus提供的SaveBatch方法       | 4674ms  | 不推荐 |      |
| MyBatis-Plus提供的InsertBatchSomeColumn方法 |  575ms  |  推荐  |      |

感谢[王菜鸟](https://blog.csdn.net/qq_44723773/article/details/128223621)提供资源
