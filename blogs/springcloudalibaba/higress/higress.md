---
title: 03-higress
date: 2028-12-15
sticky: 1
sidebar: 'auto'
tags:
 - higress
categories:
 - springcloudalibaba
---


![1716961526478](./assets/1716961526478.png)



### 1, 为什么需要服务网关

![1716961419781](./assets/1716961419781.png)



**什么是服务网关：**

传统的单体架构中只需要开放一个服务给客户端调用，但是微服务架构中是将一个系统拆分成多个微服务，如果没有网关，客户端只能在本地记录每个微服务的调用地址，当需要调用的微服务数量很多时，它需要了解每个服务的接口，这个工作量很大。

![1716961206097](./assets/1716961206097.png)



**服务网关的基本功能：**

![1716961228654](./assets/1716961228654.png)



**微服务网关的作用：**

- 提供了统一访问入口，降低了服务受攻击面
- 提供了统一跨域解决方案
- 提供了统一日志记录操作，可以进行统一监控
- 提供了统一权限认证支持
- 提供了微服务限流功能，可以保护微服务，防止雪崩效应发生



**主流网关的对比与选型：**

- **Kong 网关**：Kong 的性能非常好，非常适合做流量网关，但是对于复杂系统不建议业务网关用 Kong，主要是工程性方面的考虑
- **Zuul1.x 网关**：Zuul 1.0 的落地经验丰富，但是性能差、基于同步阻塞IO，适合中小架构，不适合并发流量高的场景，因为容易产生线程耗尽，导致请求被拒绝的情况
- **gateway 网关**：功能强大丰富，性能好，官方基准测试 RPS (每秒请求数)是Zuul的1.6倍，能与 SpringCloud 生态很好兼容，单从流式编程+支持异步上也足以让开发者选择它了。
- **Higress**：一个遵循开源Ingress/Gateway API标准，提供流量调度、服务治理、安全防护三合一的高集成、易使用、易扩展、热更新的下一代云原生网关。



### 2, Higress是什么



地址：https://higress.io/zh-cn/

![1716961488590](./assets/1716961488590.png)



**Higress是什么：**

Higress是基于阿里内部的Envoy Gateway实践沉淀、以开源Istio + Envoy为核心构建的下一代云原生网关，实现了流量网关 + 微服务网关 + 安全网关三合一的高集成能力，深度集成Dubbo、Nacos、Sentinel等微服务技术栈，能够帮助用户极大的降低网关的部署及运维成本且能力不打折。



**传统网关分类：**

行业中通常把网关分为两个大类：流量网关与业务网关，流量网关主要提供全局性的、与后端业务无关的策略配置，例如阿里内部的的统一接入网关Tengine就是典型的流量网关；业务网关顾名思义主要提供独立业务域级别的、与后端业务紧耦合策略配置，随着应用架构模式从单体演进到现在的分布式微服务，业务网关也有了新的叫法 - 微服务网关（图示说明如下）。在目前容器技术与K8s主导的云原生时代，下一代网关模式依然是这样吗？

![1716961614667](./assets/1716961614667.png)





**Higress定位：**

在虚拟化时期的微服务架构下，业务通常采用流量网关 + 微服务网关的两层架构，流量网关负责南北向流量调度和安全防护，微服务网关负责东西向流量调度和服务治理，而在容器和 K8s 主导的云原生时代，Ingress 成为 K8s 生态的网关标准，赋予了网关新的使命，使得流量网关 + 微服务网关合二为一成为可能。

![1716961695906](./assets/1716961695906.png)









### 3, 安装DockerCompose



**系统环境：**

| 序号 | 软件      | 版本    |
| ---- | --------- | ------- |
| 1    | Centos    | 7.5     |
| 2    | Linux内核 | 3.8之上 |



github地址：https://github.com/docker/compose

![1716962536023](./assets/1716962536023.png)



**卸载：**

```
yum remove docker \
          docker-client \
          docker-client-latest \
          docker-common \
          docker-latest \
          docker-latest-logrotate \
          docker-logrotate \
          docker-selinux \
          docker-engine-selinux \
          docker-engine \
          docker-ce
```



**关闭防火墙:**

```
systemctl stop firewalld
```



**设置安装仓库:**

```
#安装yum的工具包
yum install -y yum-utils \
      device-mapper-persistent-data \
      lvm2 --skip-broken
#更新本地镜像源
# 设置docker镜像源
yum-config-manager \
  --add-repo \
   https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
  
sed -i 's/download.docker.com/mirrors.aliyun.com\/docker-ce/g' /etc/yum.repos.d/docker-ce.repo
#将软件包信息提前在本地索引缓存，用来提高搜索安装软件的速度,建议执行这个命令可以提升yum安装的速度。
yum makecache fast
```



**安装docker引擎:**

```
sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```



**启动docker:**

```
systemctl start docker
```



**设置docker自启动:**

```
systemctl enable docker
```



**测试docker是否安装成功:**

```
docker run hello-world
```







### 4, 部署Higress

**搭建Higress：**

在安装之前要保证Docker安装成功并且成功安装Docker Copmpose插件。

![1716968748146](./assets/1716968748146.png)



启动成功：

![1716968782848](./assets/1716968782848.png)



**启动成功后，本机端口占用情况如下：**

- 80端口：Higress 暴露，用于 HTTP 协议代理
- 443端口：Higress 暴露，用于 HTTPS 协议代理
- 15020端口：Higress 暴露，用于暴露 Prometheus 指标
- 8080端口：Higress 控制台 暴露，（admin/123456）



**Higress命令：**

![1716965025902](./assets/1716965025902.png)

- startup.sh ： 启动Higress
- shutdown.sh ： 停止Higress
- configure.sh ： 配置nacos地址



在浏览器中输入`http://103.38.81.223:8080/`，使用用户名 `admin` 和密码`admin`登录 Higress 控制台。

![1716968840820](./assets/1716968840820.png)

![1716968856120](./assets/1716968856120.png)





### 5, 创建网关微服务模块

创建模块：

![1716969628700](./assets/1716969628700.png)



引入依赖：

![1716969682600](./assets/1716969682600.png)

```xml
    <!-- springboot依赖包-->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- nacos依赖包  -->
    <dependency>
      <groupId>com.alibaba.cloud</groupId>
      <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>
```



在resources文件夹下面创建application.yml文件。

![1716969849603](./assets/1716969849603.png)

```yml
spring:
  application:
    # 应用名字
    name: order-service
  cloud:
    nacos:
      discovery:
        # Nacos注册中心的地址
        server-addr:  103.38.81.223:8848
server:
  port: 8008
```



启动类：

![1716969920941](./assets/1716969920941.png)

```java
package com.ityls;

import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@Slf4j
@EnableDiscoveryClient
@SpringBootApplication
public class OrderHigressAppcation
{
    public static void main( String[] args )
    {
        SpringApplication.run(OrderHigressAppcation.class,args);
        log.info("****************** 订单微服务启动成功 ***********");
    }
}
```



测试控制器：

![1716970049147](./assets/1716970049147.png)

```java
package com.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class IndexController {
    @GetMapping("/index")
    public String index(){
        return "hello  higress";
    }
}
```



测试：

![1716970103633](./assets/1716970103633.png)

![1716970136713](./assets/1716970136713.png)





### 6, 路由配置

在微服务架构中，路由是一种用于管理和定向请求流量的重要机制。微服务架构将一个应用程序拆分成一组小型、独立的服务，每个服务专注于执行特定的业务功能。路由在这样的环境中起到了指导请求流向的作用。

![1716970166618](./assets/1716970166618.png)



**安装Switchhosts：**

SwitchHosts是一款便捷且免费的软件，体积为8M左右，不会占用电脑过多的内存，并且默认就是绿色软件，带有简体中文界面，在windows7、xp与vista等系统中能够运行。

![1716970208384](./assets/1716970208384.png)



设置域名：

![1716970434838](./assets/1716970434838.png)

创建路由：配置支付服务路由规则。

![1716970574966](./assets/1716970574966.png)

设置路由策略：

![1716970698401](./assets/1716970698401.png)



重写地址：

![1716970681928](./assets/1716970681928.png)



需要把写好的项目，打包，上传到服务器上，然后再启动：

![1716989853858](./assets/1716989853858.png)

![1716990085570](./assets/1716990085570.png)



对应的pom文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>ityls_cloud</artifactId>
        <groupId>com.ityls</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>
    <packaging>jar</packaging>

    <artifactId>ityls-order-higress</artifactId>

    <name>ityls-order-higress</name>
    <!-- FIXME change it to the project's website -->
    <url>http://www.example.com</url>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.7</maven.compiler.source>
        <maven.compiler.target>1.7</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
            <scope>test</scope>
        </dependency>
        <!-- springboot依赖包-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>2.7.9</version>
        </dependency>

        <!-- nacos依赖包  -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
    </dependencies>

    <build>
        <!--maven的打包插件-->
        <plugins>

            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>2.7.9</version><!--  自己的SpingBoot版本   -->
                <configuration>
                    <!--  自己的启动类地址   -->
                    <mainClass>com.ityls.OrderHigressAppcation</mainClass>
                </configuration>
                <executions>
                    <execution>
                        <id>repackage</id>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>

        </plugins>
        <resources>
            <!-- pom.xml中加入resources配置 -->
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                </includes>
            </resource>
            <resource>
                <!-- 项目打包时会将java目录中的*.xml文件也进行打包 -->
                <directory>src/main/resources</directory>
            </resource>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>
    </build>

</project>
```



参考：

- https://blog.csdn.net/wangshuminjava/article/details/79911392
- https://blog.csdn.net/tq411810317/article/details/134813089



测试：

![1716990117779](./assets/1716990117779.png)



### 7, 策略配置-跨域配置



**为什么会出现跨域问题：**

出于浏览器的同源策略限制。同源策略是一种约定，它是浏览器最核心也最基本的安全功能，如果缺少了同源策略，则浏览器的正常功能可能都会受到影响。可以说Web是构建在同源策略基础之上的，浏览器只是针对同源策略的一种实现。



**什么是跨域：**

当一个请求url的**协议、域名、端口**三者之间任意一个与当前页面url不同即为跨域

| **当前页面url**             | **被请求页面url**                 | **是否跨域** | **原因**                       |
| --------------------------- | --------------------------------- | ------------ | ------------------------------ |
| <http://www.test.com/>      | <http://www.test.com/index.html>  | 否           | 同源（协议、域名、端口号相同） |
| <http://www.test.com/>      | <https://www.test.com/index.html> | 跨域         | 协议不同（http/https）         |
| <http://www.test.com/>      | <http://www.baidu.com/>           | 跨域         | 主域名不同（test/baidu）       |
| <http://www.test.com/>      | <http://blog.test.com/>           | 跨域         | 子域名不同（www/blog）         |
| <http://www.test.com:8080/> | <http://www.test.com:7001/>       | 跨域         | 端口号不同（8080/7001）        |



**跨域问题演示：**

在resources中创建templates文件夹，在编写index页面

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>

</body>
<script src="http://libs.baidu.com/jquery/2.0.0/jquery.min.js"></script>
<script>
  $.get("http://www.it.com/order/index",function(data,status){
    alert("Data: " + data );
   });
</script>
</html>
```



**问题出现:**

![1717029343598](./assets/1717029343598.png)



测试：

![1717029709225](./assets/1717029709225.png)



**Higress解决如何允许跨域:**

- 如何允许跨域，一种解决方法就是**目的域告诉请求者允许什么来源域来请求**，那么浏览器就会知道B域是否允许A域发起请求。
- CORS（"跨域资源共享"(Cross-origin resource sharing)）就是这样一种解决手段。



CORS使得浏览器在向目的域发起请求之前先发起一个OPTIONS方式的请求到目的域获取目的域的信息，比如获取目的域允许什么域来请求的信息。



配置：

![1717029543508](./assets/1717029543508.png)

![1717029579435](./assets/1717029579435.png)

再次测试：

![1717029934197](./assets/1717029934197.png)







### 8, 策略配置之什么是HTTP认证

**概述：**

HTTP认证是一种用于保护Web应用程序的一种身份验证机制。它通过在HTTP请求的头部添加认证信息，来验证用户的身份和权限。HTTP认证可以用于保护敏感信息，限制访问某些资源，或者在访问某些操作之前要求用户提供凭据。



**常见的验证方案包括：**

- Basic Authentication（基本认证）

  > Basic认证是最常见的HTTP认证方式之一。在Basic认证中，客户端发送请求时，会在请求头中包含一个"Authorization"字段，该字段包含了经过Base64编码的用户名和密码。

- Digest Authentication（摘要认证）

  > Digest认证是一种更安全的认证方式。在Digest认证中，服务器会向客户端发送一个随机数（称为"nonce"），客户端根据该随机数和用户密码计算一个摘要，并将其发送给服务器。服务器收到摘要后，会验证其有效性。Digest认证相对于Basic认证而言，更难以被中间人攻击截获密码。

- Bearer Token Authentication（令牌认证）

  > Bearer认证是一种使用令牌（Token）进行身份验证的方式。在Bearer认证中，客户端在请求头中添加一个"Authorization"字段，该字段包含了一个令牌信息。服务器在接收到请求后，会验证令牌的有效性，并根据令牌来识别用户身份。

- OAuth（开放授权）

  > OAuth认证是一种开放标准的身份验证协议，用于授权第三方应用程序访问用户资源。在OAuth认证中，用户可以通过授权服务器授权第三方应用程序访问自己的资源。这种方式可以避免用户将密码直接提供给第三方应用程序。





### 9, 策略配置-Basic 认证

![1717030209990](./assets/1717030209990.png)

**Basic 概述:**

Basic 认证是HTTP 中非常简单的认证方式，因为简单，所以不是很安全，不过仍然非常常用。当一个客户端向一个需要认证的HTTP服务器进行数据请求时，，HTTP服务器会返回401状态码，要求客户端输入用户名和密码。用户输入用户名和密码后，用户名和密码会经过BASE64加密附加到请求信息中再次请求HTTP服务器，HTTP服务器会根据请求头携带的认证信息，决定是否认证成功及做出相应的响应。



配置：

![1717030259022](./assets/1717030259022.png)



后台配置什么内容时，直接参考文档：

![1717030320986](./assets/1717030320986.png)



开始配置：

![1717030407277](./assets/1717030407277.png)

![1717030430317](./assets/1717030430317.png)

![1717030553703](./assets/1717030553703.png)

![1717030648460](./assets/1717030648460.png)

![1717030691138](./assets/1717030691138.png)

![1717030736687](./assets/1717030736687.png)

### 10, 策略配置-什么是JWT认证

**什么是JWT：**

JWT (全称：Json Web Token)是一个开放标准(RFC 7519)，它定义了一种紧凑的、自包含的方式，用于作为 JSON 对象在各方之间安全地传输信息。该信息可以被验证和信任，因为它是数字签名的。官方网址<https://jwt.io/>



**传统的session认证：**Session 的认证流程通常会像这样

![1717030821595](./assets/1717030821595.png)

**缺点：**

- **安全性**：CSRF攻击因为基于cookie来进行用户识别, cookie如果被截获，用户就会很容易受到跨站请求伪造的攻击。
- **扩展性**：对于分布式应用，需要实现 session 数据共享
- **性能**：每一个用户经过后端应用认证之后，后端应用都要在服务端做一次记录，以方便用户下次请求的鉴别，通常而言session都是保存在内存中，而随着认证用户的增多，服务端的开销会明显增大，与REST风格不匹配。因为它在一个无状态协议里注入了状态。





**JWT方式：**

![1717030881025](./assets/1717030881025.png)

**优点：**

- 无状态
- 适合移动端应用
- 单点登录友好



### 11, 策略配置-JWT原理

JWT 的原理是，服务器认证以后，生成一个 JSON 对象，发回给用户，就像下面这样。

```
{
  "姓名": "张三",
  "角色": "管理员",
  "到期时间": "2030年7月1日0点0分"
}
```



注意：用户与服务端通信的时候，都要发回这个 JSON 对象。服务器完全只靠这个对象认定用户身份。为了防止用户篡改数据，服务器在生成这个对象的时候会加上签名，服务器就不保存任何 session 数据了，也就是说，服务器变成无状态了，从而比较容易实现扩展。



**JWT 结构：**

一个 `JWT` 实际上就是一个字符串，它由三部分组成：`头部`、`载荷`与`签名`。中间用点 `.` 分隔成三个部分。注意 `JWT` 内部是没有换行的。

![1717030990150](./assets/1717030990150.png)



**头部 / header**

JSON对象，描述 JWT 的元数据。其中 alg 属性表示签名的算法（algorithm），默认是 HMAC SHA256（写成 HS256）；typ 属性表示这个令牌（token）的类型（type），统一写为 JWT。

```
{
 "alg": "HS256",
 "typ": "JWT"
}
```

注意：上面代码中，`alg`属性表示签名的算法（algorithm），默认是 HMAC SHA256（写成 HS256）；`typ`属性表示这个令牌（token）的类型（type），JWT 令牌统一写为`JWT`然后将头部进行**Base64编码**构成了第一部分，Base64是一种用64个字符来表示任意二进制数据的方法，Base64是一种任意二进制到文本字符串的编码方法，常用于在URL、Cookie、网页中传输少量二进制数据。



**载荷 / Payload**

`Payload` 部分也是一个 `JSON` 对象，用来存放实际需要传递的数据。`JWT` 指定七个默认字段供选择。除了默认字段之外，你完全可以添加自己想要的任何字段，一般用户登录成功后，就将用户信息存放在这里

```
iss：发行人
exp：到期时间
sub：主题
aud：用户
nbf：在此之前不可用
iat：发布时间
jti：JWT ID用于标识该JWT
```

```
{
 "iss": "xxxxxxx",
 "sub": "xxxxxxx",
 "aud": "xxxxxxx",
 "user": {
     'username': 'ityls',
     'userId': 1
  } 
}
```



**签名 / Signature**

- 签名部分是对上面的 头部、载荷 两部分数据进行的数据签名
- 为了保证数据不被篡改，则需要指定一个密钥，而这个密钥一般只有你知道，并且存放在服务端
- 生成签名的代码一般如下：

````
// 其中secret 是密钥
String signature = HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)
````



**JWT 的使用方式:**

![1717031235595](./assets/1717031235595.png)



**流程：**

客户端收到服务器返回的 JWT，可以储存在 Cookie 里面，也可以储存在 localStorage。此后，客户端每次与服务器通信，都要带上这个 JWT。你可以把它放在 Cookie 里面自动发送，但是这样不能跨域，所以更好的做法是放在 HTTP 请求的头信息`Authorization`字段里面。



### 12, 策略配置-创建认证中心微服务

创建模块：

![1717031527290](./assets/1717031527290.png)



```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>2.7.9</version>
        </dependency>
        <!--nacos依赖包 注册中心-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <!--jwt依赖包-->
        <dependency>
            <groupId>org.bitbucket.b_c</groupId>
            <artifactId>jose4j</artifactId>
            <version>0.7.0</version>
        </dependency>
        <!--hutool工具包-->
        <dependency>
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
            <version>5.8.18</version>
        </dependency>
    </dependencies>
```



```xml
        <!--maven的打包插件-->
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>2.7.9</version><!--  自己的SpingBoot版本   -->
                <configuration>
                    <!--  自己的启动类地址   -->
                    <mainClass>com.ityls.AuthApplication</mainClass>
                </configuration>
                <executions>
                    <execution>
                        <id>repackage</id>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
```



在resources文件夹下面创建application.yml文件。

![1717031761324](./assets/1717031761324.png)

```yml
spring:
  application:
    # 应用名字
    name: auth-service
  cloud:
    nacos:
      discovery:
        # Nacos注册中心的地址
        server-addr:  103.38.81.223:8848
server:
  port: 8989
```



启动类：

![1717031985395](./assets/1717031985395.png)

```java
package com.ityls;

import lombok.extern.slf4j.Slf4j;
import org.jose4j.lang.JoseException;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@EnableDiscoveryClient
@SpringBootApplication
@Slf4j
public class AuthApplication
{
    public static void main( String[] args ) throws JoseException {
        SpringApplication.run(AuthApplication.class,args);
        log.info("*****************  认证微服务启动成功 **************");
    }
}
```



运行项目：

![1717031964856](./assets/1717031964856.png)



测试：

![1717031976703](./assets/1717031976703.png)



jar包上传到服务器：

![1717032132862](./assets/1717032132862.png)



本地停止：

![1717032243941](./assets/1717032243941.png)



在服务器上运行：

![1717032275599](./assets/1717032275599.png)

再次测试：

![1717032311432](./assets/1717032311432.png)



### 13, 策略配置-编写JWT工具类生成JWT



生成公钥和私钥：

![1717032649261](./assets/1717032649261.png)

```java
// 生成公钥和私钥
RsaJsonWebKey rsaJsonWebKey = RsaJwkGenerator.generateJwk(2048);
// 生成公钥
String publicjson = rsaJsonWebKey.toJson(JsonWebKey.OutputControlLevel.PUBLIC_ONLY);
// 生成私钥
String privatejson = rsaJsonWebKey.toJson(JsonWebKey.OutputControlLevel.INCLUDE_PRIVATE);
System.out.println(publicjson);
System.out.println(privatejson);
```



生成token:

![1717033257960](./assets/1717033257960.png)

```java

    /**
     * 生成token
     * @param userId 用户id
     * @param username 用户名字
     * @return
     */
    public static String sign(Long userId,String username) throws JoseException {

        // 1、 创建jwtclaims  jwt内容载荷部分
        JwtClaims claims = new JwtClaims();
        // 是谁创建了令牌并且签署了它
        claims.setIssuer("abcd");
        // 令牌将被发送给谁
        claims.setAudience("audience");
        // 失效时间长 （从现在开始10分钟）
        claims.setExpirationTimeMinutesInTheFuture(10000);
        // 令牌唯一标识符
        claims.setGeneratedJwtId();
        // 当令牌被发布或者创建现在
        claims.setIssuedAtToNow();
        // 再次之前令牌无效
        claims.setNotBeforeMinutesInThePast(2);
        // 主题
        claims.setSubject("subject");
        // 可以添加关于这个主题得声明属性
        claims.setClaim("userId",userId);
        claims.setClaim("username",username);

        // 2、签名
        JsonWebSignature jws = new JsonWebSignature();
        //赋值载荷
        jws.setPayload(claims.toJson());

        // 3、jwt使用私钥签署
        PrivateKey privateKey = new RsaJsonWebKey(JsonUtil.parseJson(privatejson)).getPrivateKey();
        jws.setKey(privateKey);

        // 4、设置关键 kid
        jws.setKeyIdHeaderValue("keyId");

        // 5、设置签名算法
        jws.setAlgorithmHeaderValue(AlgorithmIdentifiers.RSA_USING_SHA256);
        // 6、生成jwt
        String jwt = jws.getCompactSerialization();

        return jwt;

    }
```



### 14, 策略配置-编写JWT工具类解密Token

![1717033359433](./assets/1717033359433.png)

```java
    /**
     * 解密token
     *
     * @param token
     */
    public static void checkJwt(String token) throws JoseException {

        // 1、引入公钥
        PublicKey publicKey = new RsaJsonWebKey(JsonUtil.parseJson(publicjson)).getPublicKey();

        // 2、使用jwtcoonsumer  验证和处理jwt
        JwtConsumer jwtConsumer = new JwtConsumerBuilder()
                .setRequireExpirationTime() //过期时间
                .setAllowedClockSkewInSeconds(30) //允许在验证得时候留有一些余地 计算时钟偏差  秒
                .setRequireSubject() // 主题生命
                .setExpectedIssuer("abcd") // jwt需要知道谁发布得 用来验证发布人
                .setExpectedAudience("audience") //jwt目的是谁 用来验证观众
                .setVerificationKey(publicKey) // 用公钥验证签名  验证密钥
                .setJwsAlgorithmConstraints(new AlgorithmConstraints(AlgorithmConstraints.ConstraintType.WHITELIST, AlgorithmIdentifiers.RSA_USING_SHA256))
                .build();
        try {
            // 3、验证jwt 并将其处理为 claims
            JwtClaims jwtClaims = jwtConsumer.processToClaims(token);
            System.out.println(jwtClaims);
        } catch (InvalidJwtException e) {
            throw new RuntimeException(e);
        }


    }
```



测试：

![1717033402594](./assets/1717033402594.png)

```java
    public static void main(String[] args) throws JoseException {
        // // 生成公钥和私钥
        // RsaJsonWebKey rsaJsonWebKey = RsaJwkGenerator.generateJwk(2048);
        // // 生成公钥
        // String publicjson = rsaJsonWebKey.toJson(JsonWebKey.OutputControlLevel.PUBLIC_ONLY);
        // // 生成私钥
        // String privatejson = rsaJsonWebKey.toJson(JsonWebKey.OutputControlLevel.INCLUDE_PRIVATE);
        // System.out.println(publicjson);
        // System.out.println(privatejson);

        // String ityls = sign(1001L, "ityls");
        // System.out.println(ityls);

        checkJwt("eyJraWQiOiJrZXlJZCIsImFsZyI6IlJTMjU2In0.eyJpc3MiOiJhYmNkIiwiYXVkIjoiYXVkaWVuY2UiLCJleHAiOjE3MTc2MzMwMDQsImp0aSI6InkwakRUd3hjYWRvTnhacTJGbmFMNmciLCJpYXQiOjE3MTcwMzMwMDUsIm5iZiI6MTcxNzAzMjg4NSwic3ViIjoic3ViamVjdCIsInVzZXJJZCI6MTAwMSwidXNlcm5hbWUiOiJpdHlscyJ9.zCGOjjai2pt1SdrIOSpVj280f3iJ7UbzP2uMCC1CbWAetqA2JNJDa0DlYR_dY6Egbh9zQUq33plPZNOFLdmpa7ABRHMMguxnH9MGLnpinmY7-4PMdAfZkHl4isc6CtWhjY6emlqvzZfHX5mP_LnyxtKwHnY3hQ1-PslVKu3_uF7OLzCTGLKG4H_d3Tx6h38rFT703GdjoK_449Sg3GOuVOG7u5_JzNoAGOieHyzMznIpsiquxSoxXoHB4I_F1ASjvymvLHoJi892FptQBWcZ6SGhccu8xArmYP0otg4KShDwDh3TT1vHERuVeGdy4ATYUEBAwkCjt0ffQIoI7-QUsA");
    }
```







### 15, 认证中心微服务颁发Token令牌



创建DTO:

![1717033675346](./assets/1717033675346.png)

```java
package com.ityls.domian;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.ToString;
import lombok.extern.slf4j.Slf4j;

@Data
@Slf4j
@ToString
@AllArgsConstructor
@NoArgsConstructor
public class LoginBodyDTO {
    private String username;//用户名
    private String password;// 密码
}
```



统一结果返回集：

![1717033732602](./assets/1717033732602.png)

```java
package com.ityls.domian;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.ToString;

/**
 * 统一结果返回集
 */
@Data
@AllArgsConstructor
@NoArgsConstructor
@ToString
public class R {

    // 状态码
    private int code;

    // 返回信息
    private String meg;

    // 数据
    private Object data;

    public static R fail(String meg){
        R r = new R();
        r.setCode(500);
        r.setMeg(meg);
        return r;
    }
    public static R ok(Object data){
        R r = new R();
        r.setCode(200);
        r.setMeg("sucess");
        r.setData(data);
        return r;
    }
}
```



controller层：

![1717033775221](./assets/1717033775221.png)



```java
package com.ityls.controller;

import com.ityls.domian.LoginBodyDTO;
import com.ityls.domian.R;
import com.ityls.service.SysLoginService;
import org.jose4j.lang.JoseException;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

/**
 * 用户控制层
 */
@RestController
public class LoginController {

    @Autowired
    private SysLoginService service;

    /**
     * 登录
     * @param loginBodyDTO 登录DTO模型
     * @return
     */
    @PostMapping("/login")
    public R login(@RequestBody LoginBodyDTO loginBodyDTO) throws JoseException {
        R login = service.login(loginBodyDTO);
        return login;
    }
}
```



service层：

![1717033837191](./assets/1717033837191.png)

```java
package com.ityls.service;

import com.alibaba.nacos.common.utils.StringUtils;
import com.ityls.domian.LoginBodyDTO;
import com.ityls.domian.R;
import com.ityls.utils.JwtUtils;
import org.jose4j.lang.JoseException;
import org.springframework.stereotype.Service;

@Service
public class SysLoginService {
    /**
     * 登录
     * @param loginBodyDTO
     * @return
     */
    public R login(LoginBodyDTO loginBodyDTO) throws JoseException {
        // 1、用户名或者密码校验
        if (StringUtils.isEmpty(loginBodyDTO.getUsername()) || StringUtils.isEmpty(loginBodyDTO.getPassword()) ){
            return R.fail("用户名或者密码为空");
        }
        // 2、判断用户名和密码是否正确
        //TODO 数据库操作
        if (loginBodyDTO.getUsername().equals("admin")&& loginBodyDTO.getPassword().equals("123456")){
            // 颁发登录token
            String token = JwtUtils.sign(1001L, "admin");
            return R.ok(token);
        }else {
            return R.fail("用户名或者密码不对");
        }
    }
}
```



测试：

![1717033896397](./assets/1717033896397.png)

![1717034010460](./assets/1717034010460.png)

![1717034024421](./assets/1717034024421.png)



服务打包上传到服务器：

![1717034251734](./assets/1717034251734.png)

![1717034265384](./assets/1717034265384.png)



### 16, Higress策略配置-JWT配置



**基于Token的认证：**

很多对外开放的API需要识别请求者的身份，并据此判断所请求的资源是否可以返回给请求者。token就是一种用于身份验证的机制，基于这种机制，应用不需要在服务端保留用户的认证信息或者会话信息，可实现无状态、分布式的Web应用授权，为应用的扩展提供了便利。

![1717034390640](./assets/1717034390640.png)



**业务流程：**

1. 客户端向API网关发起认证请求，请求中一般会携带终端用户的用户名和密码；
2. 网关将请求直接转发给后端服务；
3. 后端服务读取请求中的验证信息（比如用户名、密码）进行验证，验证通过后使用私钥生成标准的token，返回给网关；
4. 网关将携带token的应答返回给客户端，客户端需要将这个token缓存到本地；
5. 客户端向API网关发送业务请求，请求中携带token；
6. 网关使用用户设定的公钥对请求中的token进行验证，验证通过后，将请求透传给后端服务；
7. 后端服务进行业务处理后应答；
8. 网关将业务应答返回给客户端。





`jwt-auth`插件实现了基于JWT(JSON Web Tokens)进行认证鉴权的功能，支持从HTTP请求的URL参数、请求头、Cookie字段解析JWT，同时验证该Token是否有权限访问。

![1717034438726](./assets/1717034438726.png)



再创建一个路由：

![1717034552888](./assets/1717034552888.png)

![1717034581203](./assets/1717034581203.png)



测试：

![1717034647896](./assets/1717034647896.png)









网关进行配置：

![1717035108267](./assets/1717035108267.png)



在json.cn中查看：

![1717035148294](./assets/1717035148294.png)



查看我们的公钥：

![1717035316539](./assets/1717035316539.png)



补全我们的公钥：

![1717035377138](./assets/1717035377138.png)



有了公钥，开始配置：

![1717035494432](./assets/1717035494432.png)





使用：

![1717035908815](./assets/1717035908815.png)



启动订单模块：

![1717035679901](./assets/1717035679901.png)



![1717035695507](./assets/1717035695507.png)



测试：

![1717035934407](./assets/1717035934407.png)





登录得到token:

![1717035970393](./assets/1717035970393.png)



带着token去访问：

![1717036601913](./assets/1717036601913.png)





把之前的Basic认证关了：

![1717036641205](./assets/1717036641205.png)





### 17, Higress策略配置-Key 认证

**功能说明：**

`key-auth`插件实现了基于 API Key 进行认证鉴权的功能，支持从 HTTP 请求的 URL 参数或者请求头解析 API Key，同时验证该 API Key 是否有权限访问。

![1717036704390](./assets/1717036704390.png)



全局配置：

![1717036776478](./assets/1717036776478.png)





![1717036849873](./assets/1717036849873.png)



使用：

![1717036898218](./assets/1717036898218.png)



关掉之前的jwt认证。



测试：

![1717036940198](./assets/1717036940198.png)



添加Key:

![1717036989598](./assets/1717036989598.png)



也可以根据请求头，进行配置：

![1717037038776](./assets/1717037038776.png)





### 18, Higress策略配置-请求屏蔽

**功能说明：**

`request-block`插件实现了基于 URL、请求头等特征屏蔽 HTTP 请求，可以用于防护部分站点资源不对外部暴露。



配置：

![1717037200709](./assets/1717037200709.png)

![1717037249677](./assets/1717037249677.png)



![1717037295535](./assets/1717037295535.png)



测试：

![1717037311513](./assets/1717037311513.png)



放开：

![1717037326876](./assets/1717037326876.png)



测试：

![1717037341859](./assets/1717037341859.png)





也可以根据请求头进行屏蔽：

![1717037396774](./assets/1717037396774.png)









