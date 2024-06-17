---
title: 05-Config
date: 2028-12-15
sticky: 1
sidebar: 'Config'
tags:
 - Config
categories:
 - springcloud
---

### 1, Spring Cloud Config



**分布式系统面临问题：**

在分布式系统中，由于服务数量巨多，为了方便服务配置文件统一管理，实时更新，所以需要分布式配置中心组件。

![1718604491850](assets/1718604491850.png)





**什么是Spring Cloud Config：**

Spring Cloud Config项目是一个解决分布式系统的配置管理方案。

![1718604520998](assets/1718604520998.png)

整个结构包括三个部分，客户端（各个微服务应用），服务端（中介者），配置仓库（可以是本地文件系统或者远端仓库，包括git,svn等）。

- 配置仓库中放置各个配置文件（.yml 或者.properties）
- 服务端指定配置文件存放的位置
- 客户端指定配置文件的名称



**Config能干什么：**

- 提供服务端和客户端支持
- 集中管理各环境的配置文件
- 配置文件修改之后，可以快速的生效
- 可以进行版本管理
- 支持大的并发查询
- 支持各种语言



**对比主流配置中心：**

开源的配置中心有很多，比如，360的QConf、淘宝的 nacos、携程的Apollo等。在Spring Cloud中，有分布式配置中心组件spring cloud config，它功能全面、强大，可以无缝地和Spring体系相结合，使用方便简单。





### 2, Config配置总控中心搭建

服务端开发最主要的任务是配置从哪里读取对应的配置文件，我们将配置从Git仓库读取配置文件。



在码云新建一个名为springcloud-config的新的仓库

![1718604808579](assets/1718604808579.png)



![1718604858969](assets/1718604858969.png)

![1718604913675](assets/1718604913675.png)

config-dev.yml

```yml
config:
  info: "master branch,config-dev.yml version=1" 
```

config-test.yml

```yml
config:
  info: "master branch,config-test.yml version=1" 
```

config-prod.yml

```yml
config:
  info: "master branch,config-prod.yml version=1" 
```

![1718604989345](assets/1718604989345.png)





新建模块cloud-config-server3344

![1718605056984](assets/1718605056984.png)

```xml
<dependencies>
    <!--  引入Eureka client依赖  -->
    <dependency>
 <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <!--  引入 config 依赖-->
    <dependency>
 <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-config-server</artifactId>
    </dependency>
    <!--  引入 web 依赖-->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.18.22</version>
    </dependency>
  </dependencies>
```



配置：

![1718605141795](assets/1718605141795.png)

```yml
server:
  port: 3344
spring:
  application:
    name: cloud-config-center
  cloud:
    config:
      server:
        git:
          uri: https://gitee.com/tubie/cloud-config.git
          search-paths:
            - cloud-config
      label: master
eureka:
  client:
    # 表示是否将自己注册到Eureka Server
    register-with-eureka: true
    # 示是否从Eureka Server获取注册的服务信息
    fetch-registry: true
    # Eureka Server地址
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka
  instance:
    instance-id: cloud-config-center
    prefer-ip-address: true
```



启动类：

![1718605231469](assets/1718605231469.png)

```java
@SpringBootApplication
@Slf4j
//开启配置中心功能
@EnableConfigServer
//开启注册功能
@EnableEurekaClient
public class ConfigServer3344 {
    public static void main(String[] args) {
        SpringApplication.run(ConfigServer3344.class,args);
        log.info("************  ConfigServer3344 启动成功 *****");
    }
}
```



测试通过config微服务是否可以从码云上获取配置 http://locahost:3344/config-dev.yml

![1718605814991](assets/1718605814991.png)

### 3, Config配置读取规则





### 4, Config客户端配置与测试





### 5, 动态刷新



