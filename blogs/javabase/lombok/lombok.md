---
title: lombok(新版)
date: 2028-12-15
sticky: 1
sidebar: 'lombok'
tags:
 - lombok
categories:
 - javabase
---


### 1，lombok概述

以前的Java项目中，充斥着太多不友好的代码：POJO的getter/setter/toString/构造方法；打印日志；I/O流的关闭操作等等，这些代码既没有技术含量，又影响着代码的美观，Lombok应运而生。

LomBok可以通过**注解**，帮助开发人员消除JAVA中尤其是POJO类中的冗长代码。



使用LomBok之前：

![1718084836089](./assets/1718084836089.png)



使用LomBok之后：

![1718084872833](./assets/1718084872833.png)





### 2，lombok插件安装

如果IDEA版本在2020.3以上，不需要安装Lombok插件。如果IDEA版本在2020.3以下，需要安装Lombok插件，安装方法如下：

1. 点击Flie->Setting->Plugins
2. 搜索Lombok，安装

![1718084991622](./assets/1718084991622.png)



普通maven项目Lombok依赖为：

![1718085096241](./assets/1718085096241.png)

```xml
<dependency>
  <groupId>org.projectlombok</groupId>
  <artifactId>lombok</artifactId>
  <version>1.18.22</version>
  <scope>provided</scope>
</dependency>
```



SpringBoot项目Lombok的引入方式为：

```xml
<dependency>
  <groupId>org.projectlombok</groupId>
  <artifactId>lombok</artifactId>
  <optional>true</optional>
</dependency>
```



### 3，@Getter和@Setter



**作用：**为类中的属性提供setter/getter方法

**位置：**类上方或属性上方，在属性上方则为属性生成setter/getter方法，在类上方表示给该类下的所有属性生成setter/getter方法

**属性：**设置setter和getter访问权限



测试：

```java
package com.ityls.domain;

import lombok.AccessLevel;
import lombok.Getter;
import lombok.Setter;

//给类下的所有属性添加Setter/Getter
@Setter
@Getter
public class User {
    //给id属性添加Setter
    // @Setter
    private Integer id;

    //给username的setter方法设置私有权限
    // @Setter(AccessLevel.PRIVATE)
    private String username;

    //取消password的Getter方法
    @Getter(AccessLevel.NONE)
    private String password;

    private static int age;
    private final String address = null;
}
```



![1718085392019](./assets/1718085392019.png)

![1718085434760](./assets/1718085434760.png)

注：

- static修饰的变量不生成getter和setter方法
- final修饰的变量只生成getter方法



接着测试：

![1718085488906](./assets/1718085488906.png)

![1718085523370](./assets/1718085523370.png)



在IDEA中，按住`Alt+7`可以查看Lombok生成的代码



### 4，@ToString

**作用：**生成toString方法，默认情况下它会按顺序打印类名称以及每个字段。

**位置：**类上方

**属性：**exclude：取消某一个或多个变量在toString方法中的显示



代码：

```java
@Setter
@Getter
//给User2设置一个toString方法，该方法不会显示password的值
@ToString(exclude = {"password"})
public class User2 {
  private Integer id;
  private String username;
  private String password;
}
```

![1718085689753](./assets/1718085689753.png)



测试：

![1718085777692](./assets/1718085777692.png)

```java
package com.ityls.test;

import com.ityls.domain.User;

public class Test {
    public static void main(String[] args) {
        User user = new User();
        user.setId(1);
        user.setUsername("malu");
        user.setPassword("123");
        System.out.println(user);
    }
}
```



### 5，@EqualsAndHashCode



**判断两个对象是否相等：**

在Java中，调用equals()可以判断两个对象是否相等。如果类不重写该方法，则判断两个引用是否指向同一个对象。

如何重写equals()：

1. 判断两个引用是否指向同一对象
2. 判断引用是否为Null
3. 判断两个对象的实际类型是否相等，此时需要调用canEqual()
4. 判断两个对象的属性是否相等

而在Set中判断对象是否重复，在调用equals()之前，需要先调用hashCode()计算hash值。所以判断对象相等需要重写equals()、canEqual()、hashCode()三个方法。



**@EqualsAndHashCode**

**作用：**生成equals和hashCode、canEqual方法。用于比较两个类对象是否相同。

**位置：**类上方

**属性：**

- exclude： 比较时排除一些属性
- of： 比较时只使用一些属性



代码演示：

![1718086004457](./assets/1718086004457.png)

```java
@Setter
@Getter
@EqualsAndHashCode
public class User {
    private Integer id;
    private String username;
    private String password;
}
```



测试：

![1718086083822](./assets/1718086083822.png)

```java
public class Test {
    public static void main(String[] args) {
        User user1 = new User();
        user1.setId(1);
        user1.setUsername("malu");
        user1.setPassword("123");

        User user2 = new User();
        user2.setId(1);
        user2.setUsername("malu");
        user2.setPassword("123");

        System.out.println(user1.equals(user2));
    }
}
```





exclude： 比较时排除一些属性。of： 比较时只使用一些属性

![1718086149577](./assets/1718086149577.png)



注意：

- 对比时只使用非静态属性
- 默认仅使用该类定义的属性不比较父类定义的属性



### 6，NonNull



**作用：**用于方法参数前，表示调用该方法时参数不能为null；用于属性上方，表示为该属性赋值时值不能为null。

**位置：**方法参数前或属性上方。



代码：

![1718086255973](./assets/1718086255973.png)

```java
@Setter
@Getter
public class User {
    // 调用构造方法或setter给id赋值时，值不能为null
    @NonNull
    private Integer id;
    private String username;
    private String password;

    // 调用sleep方法时，time参数不能为null
    public void sleep(@NonNull Integer time){
        System.out.println("睡觉");
    }
}
```



测试：

![1718086317433](./assets/1718086317433.png)

![1718086352220](./assets/1718086352220.png)





### 7，@NoArgsConstructor

**作用：**生成无参构造方法

**位置：**类上方



代码：

![1718086468361](./assets/1718086468361.png)



### 8，@RequiredArgsConstructor

**作用：**生成包含final和@NonNull修饰的属性的构造方法

**位置：**类上方



代码：

![1718086552967](./assets/1718086552967.png)



### 9，@AllArgsConstructor

**作用：**生成全参的构造方法

**位置：**类上方



代码：

![1718086606967](./assets/1718086606967.png)



### 10，@Data

**作用：**相当于同时添加@Setter、@Getter、@ToString、@EqualsAndHashCode、 @RequiredArgsConstructor五个注解



### 11，@Builder

**作用：**提供链式风格创建对象

**位置：**类上方

![1718086759935](./assets/1718086759935.png)

```java
// 同时提供@Setter、@Getter、@ToString、@EqualsAndHashCode、@RequiredArgsConstructor
@Data
// 提供链式风格创建对象
@Builder
public class User {
    @NonNull
    private Integer id;
    private String username;
    private String password;
}
```



### 12，@Log

**作用：**在类中生成日志对象，在方法中可以直接使用

**位置：**类上方



代码：

![1718087028875](./assets/1718087028875.png)



```java
@Data
public class User {
    private Integer id;
    private String username;
    private String password;

    private Logger log = Logger.getLogger("user");

    public void sleep(){
        log.info("调用睡觉方法");
        System.out.println("睡觉");
    }
}
```



使用@Log

![1718087105405](./assets/1718087105405.png)



针对不同的日志实现产品，有不同的日志注解，使用`@Log`表示使用Java自带的日志功能，除了`@Log`，还可以使用`@Log4j`、`@Log4j2`、`@Slf4j`等注解，来使用不同的日志产品。



### 13，@Cleanup   @SneakyThrows

**@Cleanup**

**作用：**自动关闭资源，如IO流对象。

**位置：**代码前方



**@SneakyThrows:**

**作用：**对方法中异常进行捕捉并抛出

**位置：**方法上方



代码：

![1718087307048](./assets/1718087307048.png)

```java
@SneakyThrows
public void read() {
  @Cleanup FileInputStream fis = new FileInputStream("");
}
```

