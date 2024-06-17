---
title: 04-Gateway
date: 2028-12-15
sticky: 1
sidebar: 'Gateway'
tags:
 - Gateway
categories:
 - springcloud
---

### 1, 网关在微服务中的应用

没有服务网关：

![1718343281294](./assets/1718343281294.png)

**问题：**

1. 地址太多
2. 安全性
3. 管理问题



**为什么要使用服务网关：**网关是微服务架构中不可或缺的部分。使用网关后，客户端和微服务之间的网络结构如下。

![1718343328350](./assets/1718343328350.png)

**注意：**

网关统一向外部系统（如访问者、服务）提供REST API。在SpringCloud 中，使用Zuul、Spring Cloud Gateway等作为API Gateway来实现动态路由、监控、回退、安全等功能。



**认识Spring Cloud Gateway：**

Spring Cloud Gateway 是 Spring Cloud生态系统中的网关，它是基于Spring 5.0、SpringBoot 2.0和Project Reactor等技术开发的，旨在为微服务架构提供一种简单有效的、统一的API路由管理方式，并为微服务架构提供安全、监控、指标和弹性等功能。其目标是替代Zuul。

![1718343369870](./assets/1718343369870.png)

**注意：**

Spring Cloud Gateway 用"Netty + Webflux"实现，不要加入Web依赖，否则会报错，它需要加入Webflux依赖。



**什么是WebFlux：**

Webflux模式替换了旧的Servlet线程模型。用少量的线程处理request和response io操作，这些线程称为Loop线程，而业务交给响应式编程框架处理，响应式编程是非常灵活的，用户可以将业务中阻塞的操作提交到响应式框架的work线程中执行，而不阻塞的操作依然可以在Loop线程中进行处理，大大提高了Loop线程的利用率。

![1718343410804](./assets/1718343410804.png)

**注意：**

Webflux虽然可以兼容多个底层的通信框架，但是一般情况下，底层使用的还是Netty，毕竟，Netty是目前业界认可的最高性能的通信框架。而Webflux的Loop线程，正好就是著名的Reactor模式IO处理模型的Reactor线程，如果使用的是高性能的通信框架Netty。



**温馨提示：**

什么是Netty，Netty 是一个基于NIO的客户、服务器端的编程框架。提供异步的、事件驱动的网络应用程序框架和工具，用以快速开发高性能、高可靠性的网络服务器和客户端程序。



**Spring Cloud Gateway特点：**

- 易于编写谓词( Predicates )和过滤器（ Filters ) 。其Predicates和Filters可作用于特定路由。
- 支持路径重写。
- 支持动态路由。
- 集成了Spring Cloud DiscoveryClient。





### 2, 三个核心概念

![1718343652973](./assets/1718343652973.png)

**路由(Route)：**

这是网关的基本构建块。它由一个ID，一个目标URI，一组断言和一组过滤器定义。如果断言为真，则路由匹配。



**断言(predicate)：**

输入类型是一个ServerWebExchange。我们可以使用它来匹配来自HTTP请求的任何内容，例如headers或参数。



**过滤(filter)：**

可以在请求被路由前或者之后对请求进行修改。



![1718343666908](./assets/1718343666908.png)

**举个例子：**

你想去动物园游玩，那么你买了一张熊猫馆的门票，只能进入熊猫馆的区域，而不能去犀牛管瞎转。因为没有犀牛馆的门票，进不去，就算走到门口，机器也不能识别。这里门票就相当于请求URL，熊猫馆相当于**路由**，而门口识别门卡的机器就是**断言**。然后我进入熊猫馆里面看到工作人员我想很有可能是**过滤器**，结果还真是，他在我进入馆之前拿手持设备对我全身扫描看看有没有危险品(请求前改代码)，并且在我出熊猫馆之后要求再次检察看看我是否携带熊猫出馆(请求后改动代码)。这个工作人员相当于**过滤器**。



**总结：**

首先任何请求进来，网关都会把它们拦住。根据请求的URL把它们分配到不同的**路由**上，**路由**上面会有**断言**，来判断请求能不能进来。进来之后会有一系列的**过滤器**对请求被转发前或转发后进行改动。 具体怎么个改动法，那就根据业务不同而自定义了。一般就是监控，限流，日志输出等等。



### 3, 入门案例

创建cloud-gateway-gateway9527工程

![1718344195105](./assets/1718344195105.png)



依赖：

```xml
<dependencies>
    <!--  引入网关Gateway依赖   -->
    <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-gateway</artifactId>
    </dependency>
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.18.22</version>
    </dependency>
  </dependencies>
```



主启动类:

![1718344331855](./assets/1718344331855.png)

```java
@Slf4j
@SpringBootApplication
public class GatewayMain {
  public static void main(String[] args) {
    SpringApplication.run(GatewayMain.class,args);
    log.info("********** GatewayMain 服务启动成功 *********");
   }
}
```



配置：

![1718344468538](./assets/1718344468538.png)

```yml
server:
  port: 9527
spring:
  cloud:
    gateway:
      routes:
        # 路由ID，没有固定规则但要求唯一，建议配合服务名
        - id: cloud-openfeign-order-consumer
          # 匹配后提供服务的路由地址
          uri: http://localhost:80
          # 断言
          predicates:
            # 路径相匹配的进行路由
            - Path=/order/*
```



**测试:**

- 启动注册中心 7001,7002
- 启动服务提供者8001
- 启动网关服务9527

请求：http://localhost:9527/payment/index

![1718344587549](./assets/1718344587549.png)











### 4, JAVA API构建路由

![1718344614702](./assets/1718344614702.png)



代码注入RouteLocator:

![1718344735549](./assets/1718344735549.png)

```java
@Configuration
public class GatewayConfig {
    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder routeLocatorBuilder){
        RouteLocatorBuilder.Builder routes = routeLocatorBuilder.routes();
        //设置路由
        routes.route("path_rote",r -> r.path("/guonei").uri("http://news.baidu.com/guonei")).build();
        return routes.build();
    }
}
```



测试：

![1718344888798](./assets/1718344888798.png)



### 5, 路由规则

Gateway 的路由规则主要有三个部分，分别是路由、断言(谓词)和过滤器。

![1718347536763](./assets/1718347536763.png)



**路由：**路由是 Gateway 的⼀个基本单元。



**断言：**也称谓词，实际上是路由的判断规则。一个路由中可以添加多个谓词的组合。



**提示：**

- 打个比方，你可以为某个路由设置⼀条谓词规则，约定访问路径的匹配规则为
- Path=/bingo/*，在这种情况下只有以 /bingo 打头的请求才会被当前路由选中。



**过滤器：**

![1718347631234](./assets/1718347631234.png)

Gateway 组件使用了⼀种 FilterChain的模式对请求进行处理，每⼀个服务请求（Request）在发送到目标标服务之前都要被⼀串 FilterChain处理。同理，在 Gateway接收服务响应（Response）的过程中也会被 FilterChain 处理⼀把。





### 6, 动态路由



创建payment8002，依赖使用的和8001是一样的。

![1718347919075](./assets/1718347919075.png)



配置：

![1718347998122](./assets/1718347998122.png)

![1718348019465](./assets/1718348019465.png)

![1718348127851](./assets/1718348127851.png)

```java
    @GetMapping("/lb")
    public String lb(){
        return port;
    }
```



启动：

![1718348309925](./assets/1718348309925.png)

![1718348328807](./assets/1718348328807.png)



默认情况下Gateway会根据注册中心的服务列表，以注册中心上微服务名为路径创建动态路由进行转发，从而实现动态路由的功能。



**添加eureka依赖：**

![1718348412889](./assets/1718348412889.png)

```xml
<!--  引入Eureka client依赖  -->
<dependency>
   <groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```



启动类：

![1718348855987](./assets/1718348855987.png)



**添加yml配置:**

![1718348789896](./assets/1718348789896.png)

```yml
server:
  port: 9527
eureka:
  client:
    service-url:
      # Eureka server 地址
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka
  instance:
    #根据需要自己起名字
    instance-id: cloud-gateway-gateway9527
spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      routes:
        # 路由ID，没有固定规则但要求唯一，建议配合服务名
        - id: cloud-openfeign-order-consumer
          # 匹配后提供服务的路由地址
          uri: http://localhost:80
          # 断言
          predicates:
            # 路径相匹配的进行路由
            - Path=/order/*
        - id: cloud-payment-provider
          # # 匹配后提供服务的路由地址 lb后跟提供服务的微服务的名
          uri: lb://CLOUD-PAYMENT-PROVIDER
          # 断言
          predicates:
            # 路径相匹配的进行路由
            - Path=/payment/*
```



**测试：**

- 启动eureka服务注册发现 7001,7002
- 启动服务提供者payment8001,8002
- 启动网关服务测试



![1718348899659](./assets/1718348899659.png)

![1718348958787](./assets/1718348958787.png)



### 7, 断言

一个请求在抵达网关层后，首先就要进行断言匹配，在满足所有断言之后才会进入Filter阶段。说白了Predicate就是一种路由规则，通过Gateway中丰富的内置断言的组合，我们就能让一个请求找到对应的Route来处理。



**After路由断言 Factory：**

After Route Predicate Factory采用一个参数——日期时间。在该日期时间之后发生的请求都将被匹配。

```yml
predicates:
  # 路径相匹配的进行路由
  - Path=/payment/**
  - After=2030-02-15T14:54:23.317+08:00[Asia/Shanghai]  # UTC时间格式的时间参数。
```

UTC时间格式的时间参数时间生成方法

```java
public static void main(String[] args) {
  ZonedDateTime now = ZonedDateTime.now();
  System.out.println(now);
}
```



**Before路由断言 Factory:**

Before Route Predicate Factory采用一个参数——日期时间。在该日期时间之前发生的请求都将被匹配。

yml文件添加配置：

```yml
predicates:
  - Before=2030-02-15T14:54:23.317+08:00[Asia/Shanghai]
```



**Between 路由断言 Factory：**

Between 路由断言 Factory有两个参数，datetime1和datetime2。在datetime1和datetime2之间的请求将被匹配。datetime2参数的实际时间必须在datetime1之后。

yml文件添加配置：

```yml
predicates:
  - Between=2030-02-15T14:54:23.317+08:00[Asia/Shanghai],2030-02-15T14:54:23.317+08:00[Asia/Shanghai]
```



**Cookie路由断言 Factory:**

顾名思义，Cookie验证的是Cookie中保存的信息，Cookie断言和上面介绍的两种断言使用方式大同小异，唯一的不同是它必须连同属性值一同验证，不能单独只验证属性是否存在。

yml文件添加配置：

```yml
predicates:
  # 路径相匹配的进行路由
   - Cookie=username,zzyy
```

![1718349644527](./assets/1718349644527.png)



**Header路由断言 Factory:**

这个断言会检查Header中是否包含了响应的属性，通常可以用来验证请求是否携带了访问令牌。

yml文件添加配置：

```yml
# 请求头要有X-Request-Id属性并且值为整数的正则表达式
predicates:
  - Header=X-Request-Id, \d+
```

![1718349624269](./assets/1718349624269.png)



**Host路由断言 Factory:**

Host 路由断言 Factory包括一个参数：host name列表。使用Ant路径匹配规则， `.`作为分隔符。访问的主机匹配http或者https, baidu.com 默认80端口, 就可以通过路由。 多个`,`号隔开。

yml文件添加配置：

```yml
predicates:
- Host=malu.com
```

Host文件新增配置:

```
127.0.0.1 malu.com
```



**Method路由断言 Factory:**

这个断言是专门验证HTTP Method的，在下面的例子中，我们把Method断言和Path断言通过一个and连接符合并起来，共同作用于路由判断，当我们访问“/gateway/sample”并且HTTP Method是GET的时候，将适配下面的路由。

yml文件添加配置：

```yml
spring:
  cloud:
   gateway:
    routes:
    # 路由ID，没有固定规则但要求唯一，建议配合服务名
     - id: payment_provider
     # 匹配后提供服务的路由地址
      uri: lb://CLOUD-PAYMENT-PROVIDER
     # 断言
      predicates:
      # 路径相匹配的进行路由
       - Path=/payment/**
       - Method=GET
```



**Query路由断言 Factory:**

请求断言也是在业务中经常使用的，它会从ServerHttpRequest中的Parameters列表中查询指定的属性，有如下两种不同的使用方式。

yml文件添加配置：

```yml
spring:
  cloud:
   gateway:
    routes:
    # 路由ID，没有固定规则但要求唯一，建议配合服务名
     - id: payment_provider
     # 匹配后提供服务的路由地址
      uri: lb://CLOUD-PAYMENT-PROVIDER
     # 断言
      predicates:
      # 路径相匹配的进行路由
       - Path=/payment/**
      # 要有参数名称并且是正整数才能路由
       - Query=username,\d+
```

![1718349592956](./assets/1718349592956.png)





### 8, 过滤器

微服务系统中的服务非常多。如果每个服务都自己做鉴权、限流、日志输出，则非常不科学。所以，可以通过网关的过滤器来处理这些工作。在用户访问各个服务前，应在网关层统一做好鉴权、限流等工作。



**Filter的生命周期:**

根据生命周期可以将Spring Cloud Gateway中的Filter分为"PRE"和"POST"两种。

- **PRE**：代表在请求被路由之前执行该过滤器，此种过滤器可用来实现参数校验、权限校验、流量监控、日志输出、协议转换等功能。
- **POST**：代表在请求被路由到微服务之后执行该过滤器。此种过滤器可用来实现响应头的修改（如添加标准的HTTP Header )、收集统计信息和指标、将响应发送给客户端、输出日志、流量监控等功能。



**Filter分类:**

根据作用范围，Filter可以分为以下两种。

- GatewayFilter：网关过滤器，此种过滤器只应用在单个路由或者一个分组的路由上。
- GlobalFilter：全局过滤器，此种过滤器会应用在所有的路由上。



### 9, 网关过滤器

网关过滤器( GatewayFilter )允许以某种方式修改传入的HTTP请求,或输出的HTTP响应。网关过滤器作用于特定路由。Spring Cloud Gateway内置了许多网关过滤器工厂来编写网关过滤器。

| 过滤器工厂                  | 作用                                                         | 参数                                                         |
| --------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| AddRequestHeader            | 为原始请求添加Header                                         | Header的名称及值                                             |
| AddRequestParameter         | 为原始请求添加请求参数                                       | 参数名称及值                                                 |
| AddResponseHeader           | 为原始响应添加Header                                         | Header的名称及值                                             |
| DedupeResponseHeader        | 剔除响应头中重复的值                                         | 需要去重的Header名称及去重策略                               |
| Hystrix                     | 为路由引入Hystrix的断路器保护 HystrixCommand的名称           |                                                              |
| FallbackHeaders             | 为fallbackUri的请求头中添加具体的异常信息                    | Header的名称                                                 |
| PrefixPath                  | 为原始请求路径添加前缀                                       | 前缀路径                                                     |
| PreserveHostHeader          | 为请求添加一个preserveHostHeader=true的属性，路由过滤器会检查该属性以决定是否要发送原始的Host | 无                                                           |
| RequestRateLimiter          | 用于对请求限流，限流算法为令牌桶                             | keyResolver、rateLimiter、statusCode、denyEmptyKey、emptyKeyStatus |
| RedirectTo                  | 将原始请求重定向到指定的URL                                  | http状态码及重定向的url                                      |
| RemoveHopByHopHeadersFilter | 为原始请求删除IETF组织规定的一系列Header                     | 默认就会启用，可以通过配置指定仅删除哪些Header               |
| RemoveRequestHeader         | 为原始请求删除某个Header                                     | Header名称                                                   |
| RemoveResponseHeader        | 为原始响应删除某个Header                                     | Header名称                                                   |
| RewritePath                 | 重写原始的请求路径                                           | 原始路径正则表达式以及重写后路径的正则表达式                 |
| RewriteResponseHeader       | 重写原始响应中的某个Header                                   | Header名称，值的正则表达式，重写后的值                       |
| SaveSession                 | 在转发请求之前，强制执行WebSession::save操作                 | 无                                                           |
| secureHeaders               | 为原始响应添加一系列起安全作用的响应头                       | 无，支持修改这些安全响应头的值                               |
| SetPath                     | 修改原始的请求路径                                           | 修改后的路径                                                 |
| SetResponseHeader           | 修改原始响应中某个Header的值                                 | Header名称，修改后的值                                       |
| SetStatus                   | 修改原始响应的状态码                                         | HTTP 状态码，可以是数字，也可以是字符串                      |
| StripPrefix                 | 用于截断原始请求的路径                                       | 使用数字表示要截断的路径的数量                               |
| Retry                       | 针对不同的响应进行重试                                       | retries、statuses、methods、series                           |
| RequestSize                 | 设置允许接收最大请求包的大小。如果请求包大小超过设置的值，则返回 413 Payload Too Large | 请求包大小，单位为字节，默认值为5M                           |
| ModifyRequestBody           | 在转发请求之前修改原始请求体内容                             | 修改后的请求体内容                                           |
| ModifyResponseBody          | 修改原始响应体的内容                                         | 修改后的响应体内容                                           |
| Default                     | 为所有路由添加过滤器                                         | 过滤器工厂名称及值                                           |



**常用内置过滤器的使用:** 配置文件中加入SetStatus过滤器

```yml
#过滤器，请求在传递过程中可以通过过滤器对其进行一定的修改
filters: 
  - SetStatus=250 # 修改原始响应的状态码
```



配置：

![1718350048159](./assets/1718350048159.png)



### 10, 自定义网关过滤器

![1718350141563](./assets/1718350141563.png)

需求：通过过滤器，配置是否在控制台输出日志信息，以及是否记录日志。实现步骤：

- 1、类名必须叫做XxxGatewayFilterFactory，注入到Spring容器后使用时的名称就叫做Xxx。
- 2、创建一个静态内部类Config， 里面的属性为配置文件中配置的参数， - 过滤器名称=参数1,参数2…
- 2、类必须继承 AbstractGatewayFilterFactory，让父类帮实现配置参数的处理。
- 3、重写shortcutFieldOrder()方法，返回List参数列表为Config中属性集合

return Arrays.asList(“参数1”,参数2…)

- 4、无参构造方法中super(Config.class)
- 5、编写过滤逻辑 public GatewayFilter apply(Config config)



自定义一个过滤器工厂，实现里面的方法：

![1718350261567](./assets/1718350261567.png)

```java
/**
 * 自定义局部过滤器
 */
@Component
public class LogGatewayFilterFactory extends AbstractGatewayFilterFactory<LogGatewayFilterFactory.Config> {
    public LogGatewayFilterFactory(){
        super(Config.class);
    }

    @Override
    public List<String> shortcutFieldOrder() {
        return Arrays.asList("consoleLog");
    }

    @Override
    public GatewayFilter apply(Config config) {
        return ((exchange, chain) -> {
            if (config.consoleLog) {
                System.out.println("console日志已开启...");
            }
            return chain.filter(exchange);
        });
    }

    @Data
    public static class Config{
        private boolean consoleLog;
    }
}
```



配置：

![1718350366015](./assets/1718350366015.png)

```yml
# 过滤器，请求在传递过程中可以通过过滤器对其进行一定的修改
filters: 
  # 控制日志是否开启
  - Log=true
```



请求http://localhost:9527/payment/lb

![1718350479435](./assets/1718350479435.png)



### 11, 自定义全局网关过滤器

全局过滤器作用于所有路由，无需配置。通过全局过滤器可以实现对权限的统一校验，安全性验证等功能。



**内置的全局过滤器:**

SpringCloud Gateway内部也是通过一系列的内置全局过滤器对整个路由转发进行处理的。

- 路由过滤器（Forward）
- 路由过滤器（LoadBalancerClient）
- Netty路由过滤器
- Netty写响应过滤器（Netty Write Response F）
- RouteToRequestUrl 过滤器
- 路由过滤器 (Websocket Routing Filter)
- 网关指标过滤器（Gateway Metrics Filter)
- 组合式全局过滤器和网关过滤器排序（Combined Global Filter and GatewayFilter Ordering）
- 路由（Marking An Exchange As Routed）





**自定义全局过滤器:**   

![1718350659531](./assets/1718350659531.png)

开发中的鉴权逻辑:(对于验证用户是否已经登录及鉴权的过程，可以在网关统一校验)

- 当客户端第一次请求服务时，服务端对用户进行信息认证（登录）
- 认证通过，将用户信息进行加密形成token，返回给客户端，作为登录凭证
- 以后每次请求，客户端都携带认证的token
- 服务端对token进行解密，判断是否有效。



下面我们通过自定义一个GlobalFIlter，去校验所有请求的请求参数中是否包含“token”，如果不包含请求参数“token”则不转发路由，否则执行正常逻辑。

![1718351163248](./assets/1718351163248.png)

```java
import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.core.Ordered;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Component;
import org.springframework.util.StringUtils;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

/**
 * 自定义全局过滤器，需要实现GlobalFilter和Ordered接口
 */
@Component
public class AuthGlobalFilter implements GlobalFilter, Ordered {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        String token = exchange.getRequest().getQueryParams().getFirst("token");
        if (StringUtils.isEmpty(token)) {
            System.out.println("鉴权失败。确少token参数。");
            exchange.getResponse().setStatusCode(HttpStatus.UNAUTHORIZED);
            return exchange.getResponse().setComplete();
        }

        if (!"jack".equals(token)) {
            System.out.println("token无效...");
            exchange.getResponse().setStatusCode(HttpStatus.UNAUTHORIZED);
            return exchange.getResponse().setComplete();
        }

        // 继续执行filter链
        return chain.filter(exchange);
    }

    /**
     * 顺序，数值越小，优先级越高
     * @return
     */
    @Override
    public int getOrder() {
        return 0;
    }
}
```



测试：

![1718351344475](./assets/1718351344475.png)





### 12, 解决跨域

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



编写index页面：

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

  $.get("http://localhost:9527/payment/lb?token=jack",function(data,status){
    alert("Data: " + data + "\nStatus: " + status);
   });
</script>
</html>
```



出现问题：

![1718351482040](./assets/1718351482040.png)



**Gateway解决如何允许跨域：**CORS

- 如何允许跨域，一种解决方法就是**目的域告诉请求者允许什么来源域来请求**，那么浏览器就会知道B域是否允许A域发起请求。
- CORS（"跨域资源共享"(Cross-origin resource sharing)）就是这样一种解决手段。

CORS使得浏览器在向目的域发起请求之前先发起一个OPTIONS方式的请求到目的域获取目的域的信息，比如获取目的域允许什么域来请求的信息。

```yml
spring:
  cloud:
   gateway:
    globalcors:
     cors-configurations:
      '[/**]':
       allowCredentials: true
       allowedOriginPatterns: "*"
       allowedMethods: "*"
       allowedHeaders: "*"
     add-to-simple-url-handler-mapping: true
```



配置：

![1718597345981](./assets/1718597345981.png)

```yml
server:
  port: 9527
eureka:
  client:
    service-url:
      # Eureka server 地址
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka
  instance:
    #根据需要自己起名字
    instance-id: cloud-gateway-gateway9527
spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      globalcors:
        cors-configurations:
          '[/**]':
            allowCredentials: true
            allowedOriginPatterns: "*"
            allowedMethods: "*"
            allowedHeaders: "*"
        add-to-simple-url-handler-mapping: true
      routes:
        # 路由ID，没有固定规则但要求唯一，建议配合服务名
        - id: cloud-openfeign-order-consumer
          # 匹配后提供服务的路由地址
          uri: http://localhost:80
          # 断言
          predicates:
            # 路径相匹配的进行路由
            - Path=/order/*
        - id: cloud-payment-provider
          # # 匹配后提供服务的路由地址 lb后跟提供服务的微服务的名
          uri: lb://CLOUD-PAYMENT-PROVIDER
          # 断言
          predicates:
            # 路径相匹配的进行路由
            - Path=/payment/*
          filters:
            - Log=true
```







### 13, 实现用户鉴权_什么是JWT

**什么是JWT:**

JWT是一种用于双方之间传递安全信息的简洁的、[URL](https://so.csdn.net/so/search?q=URL&spm=1001.2101.3001.7020)安全的声明规范。定义了一种简洁的，自包含的方法用于通信双方之间以Json对象的形式安全的传递信息。特别适用于分布式站点的单点登录（SSO）场景。



**传统的session认证:**

每次提到无状态的 JWT 时相信都会看到另一种基于 Session 的用户认证方案介绍，这里也不例外，Session 的认证流程通常会像这样：

![1718597513438](./assets/1718597513438.png)



**缺点：**

- **安全性**：CSRF攻击因为基于cookie来进行用户识别, cookie如果被截获，用户就会很容易受到跨站请求伪造的攻击。
- **扩展性**：对于分布式应用，需要实现 session 数据共享
- **性能**：每一个用户经过后端应用认证之后，后端应用都要在服务端做一次记录，以方便用户下次请求的鉴别，通常而言session都是保存在内存中，而随着认证用户的增多，服务端的开销会明显增大，与REST风格不匹配。因为它在一个无状态协议里注入了状态。



**JWT方式:**

![1718597544289](./assets/1718597544289.png)



**优点：**

- 无状态
- 适合移动端应用
- 单点登录友好







### 14, 实现用户鉴权_JWT原理

JWT 的原理是，服务器认证以后，生成一个 JSON 对象，发回给用户，就像下面这样。

```
{
 "姓名": "张三",
 "角色": "管理员",
 "到期时间": "2030年7月1日0点0分"
}
```

用户与服务端通信的时候，都要发回这个 JSON 对象。服务器完全只靠这个对象认定用户身份。为了防止用户篡改数据，服务器在生成这个对象的时候会加上签名，服务器就不保存任何 session 数据了，也就是说，服务器变成无状态了，从而比较容易实现扩展。



**JWT的结构:**

![1718597601396](./assets/1718597601396.png)

它是一个很长的字符串，中间用点（`.`）分隔成三个部分。注意，JWT 内部是没有换行的，这里只是为了便于展示，将它写成了几行。JWT 的三个部分依次如下:

- 头部（header)
- 载荷（payload)
- 签证（signature)



**Header:**

JSON对象，描述 JWT 的元数据。其中 alg 属性表示签名的算法（algorithm），默认是 HMAC SHA256（写成 HS256）；typ 属性表示这个令牌（token）的类型（type），统一写为 JWT。

```
{
 "alg": "HS256",
 "typ": "JWT"
}
```

上面代码中，`alg`属性表示签名的算法（algorithm），默认是 HMAC SHA256（写成 HS256）；`typ`属性表示这个令牌（token）的类型（type），JWT 令牌统一写为`JWT`然后将头部进行**Base64编码**构成了第一部分，Base64是一种用64个字符来表示任意二进制数据的方法，Base64是一种任意二进制到文本字符串的编码方法，常用于在URL、Cookie、网页中传输少量二进制数据。





**Payload:**

**内容又可以分为3种标准**

- 标准中注册的声明

- 公共的声明

- 私有的声明

  

**payload-标准中注册的声明 (建议但不强制使用) ：**

- **iss**: jwt签发者
- **sub**: jwt所面向的用户
- **aud**: 接收jwt的一方
- **exp**: jwt的过期时间，这个过期时间必须要大于签发时间
- **nbf**: 定义在什么时间之前，该jwt都是不可用的.
- **iat**: jwt的签发时间
- **jti**: jwt的唯一身份标识，主要用来作为一次性token,从而回避重放攻击。



**payload-公共的声明 ：**

公共的声明可以添加任何的信息。一般这里我们会存放一下用户的基本信息（非敏感信息）。



**payload-私有的声明 ：**

私有声明是提供者和消费者所共同定义的声明。需要注意的是，**不要存放敏感信息，不要存放敏感信息，不要存放敏感信息**！！！



因为：这里**也是base64编码，任何人获取到jwt之后都可以解码！！**

```
{
 "sub": "1234567890",
 "name": "John Doe",
 "iat": 1516239022
}
```

sub和iat是标准声明，分别代表所面向的用户和jwt签发时间。

- **sub**：这个是发给一个账号是1234567890的用户（也许是ID）
- **name**：名字叫John Doe
- **iat**：签发时间是1516239022（2030/1/18 9:30:22）



**Signature:**

这部分就是 JWT 防篡改的精髓，其值是对前两部分base64UrlEncode 后使用指定算法签名生成，以默认 HS256 为例，指定一个密钥（secret），就会按照如下公式生成：

```
HMACSHA256(
 base64UrlEncode(header) + "." + base64UrlEncode(payload),
 secret,
)
```

算出签名以后，把 Header、Payload、Signature 三个部分拼成一个字符串，每个部分之间用"点"（`.`）分隔，就可以返回给用户。



![1718597878653](./assets/1718597878653.png)





**JWT 的使用方式:**

![1718597791502](./assets/1718597791502.png)

客户端收到服务器返回的 JWT，可以储存在 Cookie 里面，也可以储存在 localStorage。此后，客户端每次与服务器通信，都要带上这个 JWT。你可以把它放在 Cookie 里面自动发送，但是这样不能跨域，所以更好的做法是放在 HTTP 请求的头信息`Authorization`字段里面。









### 15, 实现用户鉴权_用户微服务

创建cloud-auth-user6500工程：

![1718598029020](./assets/1718598029020.png)

```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- redis -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        <!-- eureka client 依赖 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.22</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
    </dependencies>
```





### 16, 实现用户鉴权_JWT工具类



引入JWT依赖:

![1718598208258](./assets/1718598208258.png)

```xml
 <dependency>
   <groupId>com.alibaba</groupId>
   <artifactId>fastjson</artifactId>
   <version>1.2.79</version>
</dependency>
<dependency>
   <groupId>com.auth0</groupId>
   <artifactId>java-jwt</artifactId>
   <version>3.7.0</version>
</dependency>
```



创建JWT工具类JWTUtils:

```java
public class JWTUtil {
  // 秘钥
  public static final String SECRET_KEY = "erbadagang-123456";
  // token过期时间
  public static final long TOKEN_EXPIRE_TIME = 5 * 60 * 1000;
  // 签发人
  private static final String ISSUER = "issuer";
  // 用户名
  private static final String USER_NAME = "username";
  
}
```



生成签名方法:

```java
  public static String token(String username) {
    Date now = new Date();
    //SECRET_KEY是用来加密数据签名秘钥
    Algorithm algorithm = Algorithm.HMAC256(SECRET_KEY);
    String token = JWT.create()
        // 签发人
         .withIssuer(ISSUER)
        // 签发时间
         .withIssuedAt(now)
        // 过期时间
         .withExpiresAt(new Date(now.getTime() + TOKEN_EXPIRE_TIME))
        // 保存权限标记
         .withClaim(USER_NAME, username)
         .sign(algorithm);


    log.info("jwt generated user={}", username);
    return token;
   }
```



验证签名:

```java
 public static boolean verify(String token) {
    try {
      //SECRET_KEY是用来加密数据签名秘钥
      Algorithm algorithm = Algorithm.HMAC256(SECRET_KEY);
      JWTVerifier verifier = JWT.require(algorithm)
           .withIssuer(ISSUER)
           .build();
      //如果校验有问题会抛出异常。
      verifier.verify(token);
      return true;
     } catch (Exception ex) {
      ex.printStackTrace();
     }
    return false;
   }
```



测试JWT:

```java
public static void main(String[] args) {


    // 生成Token
    String token = JWTUtil.token("ityls");
    System.out.println(token);
    
    //验证Token
    boolean verify = JWTUtil.verify("eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJpc3N1ZXIiLCJleHAiOjE2NDUwNjMyNzYsImlhdCI6MTY0NTA2Mjk3NiwidXNlcm5hbWUiOiJpdGJhaXpoYW4ifQ.uKKVEMCTW0-e_bPHLyb-JY8CwRU7ciU8vP5B78lDY3s");
    System.out.println(verify);
   }
```



完整代码：

![1718598488481](./assets/1718598488481.png)

```java
@Slf4j
public class JWTUtil {
    // 秘钥
    public static final String SECRET_KEY = "erbadagang-123456";
    // token过期时间
    public static final long TOKEN_EXPIRE_TIME = 5 * 60 * 1000;
    // 签发人
    private static final String ISSUER = "issuer";
    // 用户名
    private static final String USER_NAME = "username";

    public static String token(String username) {
        Date now = new Date();
        //SECRET_KEY是用来加密数据签名秘钥
        Algorithm algorithm = Algorithm.HMAC256(SECRET_KEY);
        String token = JWT.create()
                // 签发人
                .withIssuer(ISSUER)
                // 签发时间
                .withIssuedAt(now)
                // 过期时间
                .withExpiresAt(new Date(now.getTime() + TOKEN_EXPIRE_TIME))
                // 保存权限标记
                .withClaim(USER_NAME, username)
                .sign(algorithm);
        log.info("jwt generated user={}", username);
        return token;
    }

    public static boolean verify(String token) {
        try {
            //SECRET_KEY是用来加密数据签名秘钥
            Algorithm algorithm = Algorithm.HMAC256(SECRET_KEY);
            JWTVerifier verifier = JWT.require(algorithm)
                    .withIssuer(ISSUER)
                    .build();
            //如果校验有问题会抛出异常。
            verifier.verify(token);
            return true;
        } catch (Exception ex) {
            ex.printStackTrace();
        }
        return false;
    }

    public static void main(String[] args) {
        // 生成Token
        String token = JWTUtil.token("ityls");
        System.out.println(token);

        //验证Token
        boolean verify = JWTUtil.verify("eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJpc3N1ZXIiLCJleHAiOjE3MTg1OTg3MzksImlhdCI6MTcxODU5ODQzOSwidXNlcm5hbWUiOiJpdHlscyJ9.bqR54UZGcAey9qyj0cK3Xzh7HCzeBg90e-e4TaKJkGA");
        System.out.println(verify);
    }

}
```











### 17, 实现用户鉴权_用户服务实现JWT鉴权



配置：

![1718599191655](./assets/1718599191655.png)

```yml
server:
  port: 6500
eureka:
  instance:
    # 实例名字
    instance-id: cloud-auth-user6500
  client:
    service-url:
      # 指定单机eureka server地址
      #defaultZone: http://localhost:7001/eureka/
      defaultZone: http://localhost:7001/eureka/,http://localhost:7002/eureka/
spring:
  application:
    name: cloud-auth-user
```



启动类：

```java
/**
 * 主启动类
 */
@SpringBootApplication
@Slf4j
@EnableEurekaClient
public class UserMain6500 {
    public static void main(String[] args) {
        SpringApplication.run(UserMain6500.class,args);
        log.info("************** UserMain6500 启动成功 **********");
    }
}
```



统一返回实体类：

```java
/**
 * 统一返回实体类
 */
@AllArgsConstructor
@NoArgsConstructor
@Data
@Builder
public class Result {

    // 返回状态码
    private int code;

    // 返回描述信息
    private String msg;

    // 返回token信息 令牌
    private String token;
}
```



controller:

```java
/**
 * 用户控制层
 */
@RestController
@RequestMapping("user")
public class UserController {

    /**
     * 登录
     * @param username 用户名
     * @param password 密码
     */
    @PostMapping("login")
    public Result login(String username, String password){
        // 1. 验证用户名和密码
        // TODO 模拟数据库操作
        if("admin".equals(username) && "123456".equals(password)){
            // 2. 生成令牌
            String token = JWTUtil.token();
            return  Result.builder()
                    .code(200)
                    .msg("succes")
                    .token(token).build();
        }else {
            return  Result.builder()
                    .code(500)
                    .msg("用户名或密码不正确")
                  .build();
        }

    }

}
```



工具类：

```java
@Slf4j
public class JWTUtil {
    // 秘钥
    public static final String SECRET_KEY = "erbadagang-123456";
    // token过期时间
    public static final long TOKEN_EXPIRE_TIME = 5 * 60 * 1000;
    // 签发人
    private static final String ISSUER = "issuer";
    // 用户名
    private static final String USER_NAME = "username";

    // public static String token(String username) {
    public static String token() {
        Date now = new Date();
        //SECRET_KEY是用来加密数据签名秘钥
        Algorithm algorithm = Algorithm.HMAC256(SECRET_KEY);
        String token = JWT.create()
                // 签发人
                .withIssuer(ISSUER)
                // 签发时间
                .withIssuedAt(now)
                // 过期时间
                .withExpiresAt(new Date(now.getTime() + TOKEN_EXPIRE_TIME))
                // 保存权限标记
                // .withClaim(USER_NAME, username)
                .sign(algorithm);
        // log.info("jwt generated user={}", username);
        return token;
    }

    public static boolean verify(String token) {
        try {
            //SECRET_KEY是用来加密数据签名秘钥
            Algorithm algorithm = Algorithm.HMAC256(SECRET_KEY);
            JWTVerifier verifier = JWT.require(algorithm)
                    .withIssuer(ISSUER)
                    .build();
            //如果校验有问题会抛出异常。
            verifier.verify(token);
            return true;
        } catch (Exception ex) {
            ex.printStackTrace();
        }
        return false;
    }

    public static void main(String[] args) {
        // 生成Token
        // String token = JWTUtil.token("ityls");
        // System.out.println(token);

        //验证Token
        // boolean verify = JWTUtil.verify("eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJpc3N1ZXIiLCJleHAiOjE3MTg1OTg3MzksImlhdCI6MTcxODU5ODQzOSwidXNlcm5hbWUiOiJpdHlscyJ9.bqR54UZGcAey9qyj0cK3Xzh7HCzeBg90e-e4TaKJkGA");
        // System.out.println(verify);
    }

}
```



测试：

![1718599291260](./assets/1718599291260.png)









### 18, 实现用户鉴权_网关过滤器加入JWT鉴权

![1718599366949](./assets/1718599366949.png)



配置路由：

![1718599725909](./assets/1718599725909.png)

```yml
        - id: cloud-auth-user
          uri: lb://CLOUD-AUTH-USER
          predicates:
            - Path=/user/*
```



注释掉之前的全局过滤器：

![1718599765397](./assets/1718599765397.png)



测试：

![1718599775487](./assets/1718599775487.png)



依赖：

![1718600091407](./assets/1718600091407.png)

```xml
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.79</version>
        </dependency>
        <dependency>
            <groupId>com.auth0</groupId>
            <artifactId>java-jwt</artifactId>
            <version>3.7.0</version>
        </dependency>
```



返回信息：

```java
/**
 * 返回信息
 */
@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
public class Response {
    private Integer code;
    private String message;
}
```



工具类：

```java
package com.ityls.utils;

import com.auth0.jwt.JWT;
import com.auth0.jwt.JWTVerifier;
import com.auth0.jwt.algorithms.Algorithm;
import lombok.extern.slf4j.Slf4j;

import java.util.Date;

@Slf4j
public class JWTUtils {
    // 秘钥
    public static final String SECRET_KEY = "erbadagang-123456";
    // token过期时间
    public static final long TOKEN_EXPIRE_TIME = 5 * 60 * 1000;
    // 签发人
    private static final String ISSUER = "issuer";
    // 用户名
    private static final String USER_NAME = "username";

    // public static String token(String username) {
    public static String token() {
        Date now = new Date();
        //SECRET_KEY是用来加密数据签名秘钥
        Algorithm algorithm = Algorithm.HMAC256(SECRET_KEY);
        String token = JWT.create()
                // 签发人
                .withIssuer(ISSUER)
                // 签发时间
                .withIssuedAt(now)
                // 过期时间
                .withExpiresAt(new Date(now.getTime() + TOKEN_EXPIRE_TIME))
                // 保存权限标记
                // .withClaim(USER_NAME, username)
                .sign(algorithm);
        // log.info("jwt generated user={}", username);
        return token;
    }

    public static boolean verify(String token) {
        try {
            //SECRET_KEY是用来加密数据签名秘钥
            Algorithm algorithm = Algorithm.HMAC256(SECRET_KEY);
            JWTVerifier verifier = JWT.require(algorithm)
                    .withIssuer(ISSUER)
                    .build();
            //如果校验有问题会抛出异常。
            verifier.verify(token);
            return true;
        } catch (Exception ex) {
            ex.printStackTrace();
        }
        return false;
    }

    public static void main(String[] args) {
        // 生成Token
        // String token = JWTUtil.token("ityls");
        // System.out.println(token);

        //验证Token
        // boolean verify = JWTUtil.verify("eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJpc3N1ZXIiLCJleHAiOjE3MTg1OTg3MzksImlhdCI6MTcxODU5ODQzOSwidXNlcm5hbWUiOiJpdHlscyJ9.bqR54UZGcAey9qyj0cK3Xzh7HCzeBg90e-e4TaKJkGA");
        // System.out.println(verify);
    }

}
```



哪些路径需要跳过：

![1718600507441](./assets/1718600507441.png)

```yml
org:
  my:
    jwt:
      #跳过认证的路由
      skipAuthUrls:
        - /user/login
```





创建用户鉴权全局过滤器：

![1718600321662](./assets/1718600321662.png)

```java
package com.ityls.config;

import com.alibaba.fastjson.JSONObject;
import com.ityls.common.Response;
import com.ityls.utils.JWTUtils;
import lombok.Data;
import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.Ordered;
import org.springframework.core.io.buffer.DataBuffer;
import org.springframework.http.HttpStatus;
import org.springframework.http.server.reactive.ServerHttpResponse;
import org.springframework.stereotype.Component;
import org.springframework.util.StringUtils;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;

import java.nio.charset.StandardCharsets;

/**\
* 用户鉴权全局过滤器
*/
@Data
@ConfigurationProperties("org.my.jwt")
@Component
@Slf4j
public class UserAuthGlobalFilter implements GlobalFilter, Ordered {

   private String[] skipAuthUrls;

   /**
    * 过滤器逻辑
    * @param exchange
    * @param chain
    * @return
    */
   @Override
   public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {

       // user/login
       // 获取请求url地址
       String path = exchange.getRequest().getURI().getPath();

       // 跳过不需要验证的路径
       if (null != skipAuthUrls && isSkip(path)){
           return chain.filter(exchange);
       }

       // 1. 从请求头中获取token
       String token = exchange.getRequest().getHeaders().getFirst("token");
       // 2. 判断token
       if(StringUtils.isEmpty(token)){
           // 3. 设置响应
           ServerHttpResponse response = exchange.getResponse();
           // 4. 设置响应状态码
           response.setStatusCode(HttpStatus.OK);
           // 5. 设置响应头
           response.getHeaders().add("Content-Type","application/json;charset=UTF-8");
           // 6. 创建响应对象
           Response res = new Response(200,"token 参数缺失");
           // 7. 对象转字符串
           byte[] bytes = JSONObject.toJSONString(res).getBytes(StandardCharsets.UTF_8);
           // 8. 数据流返回数据
           DataBuffer wrap = response.bufferFactory().wrap(bytes);
           return response.writeWith(Flux.just(wrap));
       }

       // 验证token
       boolean verify = JWTUtils.verify(token);
       if (!verify){
           // 3. 设置响应
           ServerHttpResponse response = exchange.getResponse();
           // 4. 设置响应状态码
           response.setStatusCode(HttpStatus.OK);
           // 5. 设置响应头
           response.getHeaders().add("Content-Type","application/json;charset=UTF-8");
           // 6. 创建响应对象
           Response res = new Response(200,"token 失效");
           // 7. 对象转字符串
           byte[] bytes = JSONObject.toJSONString(res).getBytes(StandardCharsets.UTF_8);
           // 8. 数据流返回数据
           DataBuffer wrap = response.bufferFactory().wrap(bytes);
           return response.writeWith(Flux.just(wrap));
       }

       // 如果各种判断都通过。。
       return chain.filter(exchange);
   }

   @Override
   public int getOrder() {
       return 0;
   }

   private boolean isSkip(String url){
       for(String skipurl :skipAuthUrls) {
           if (url.startsWith(skipurl)){
               return true;
           }
       }
       return false;
   }
}
```



测试：

![1718600620824](./assets/1718600620824.png)

![1718600732254](./assets/1718600732254.png)

![1718601788917](./assets/1718601788917.png)





































