1.基础使用
=  
1.1服务中介  
1.1.1导包
```
<dependencyManagement>
 <dependencies>
   <dependency>
     <groupId>org.springframework.cloud</groupId>  
     <artifactId>spring-cloud-dependencies</artifactId>  
     <version>Finchley.SR2</version>  
     <type>pom</type>  
     <scope>import</scope>
   </dependency>
 </dependencies>
</dependencyManagement>
<!-- 最新版的 eureka 服务端包 -->
<dependency>
 <groupId>org.springframework.cloud</groupId>  
 <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
<!-- 监控管理 -->
<dependency>
 <groupId>org.springframework.boot</groupId>  
 <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

1.1.2配置文件
（a）bootstrap.yml
```
spring:
application:
  name: kite-eureka-center
cloud:
  inetutils: ## 网卡设置
    ignoredInterfaces:  ## 忽略的网卡
      - docker0
      - veth.*
      - VM.*
    preferredNetworks:  ## 优先的网段
      - 192.168
```
（b）application.yml
```
server:
port: 3000
eureka:
instance:
  hostname: eureka-center
  appname: 注册中心
client:
  registerWithEureka: false # 单点的时候设置为 false 禁止注册自身
  fetchRegistry: false
  serviceUrl:
    defaultZone: http://localhost:3000/eureka
server:
  enableSelfPreservation: false
  evictionIntervalTimerInMs: 4000
```
1.1.3源码编写
（a）启动类
```java
@EnableEurekaServer//使用 Eureka Server 端功能，也就是启动为一个注册中心节点
@SpringBootApplication
public class Application {
   public static void main(String[] args) {
       SpringApplication.run(Application.class, args);
  }
}
```

运行 Application.java ，访问 http://localhost:3000 即可看到 Eureka 提供的 ui 控制台。


1.2服务提供者  
1.2.1导包
```
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<!-- eureka 客户端 -->
<dependency>
   <groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

1.2.2配置文件
（a）bootstrap.yml
```

```
（b）application.yml
```
server:
port: 3001
eureka:
instance:
  preferIpAddress: true
client:
  serviceUrl:
    defaultZone: http://localhost:3000/eureka  ## 注册到 eureka
spring:
application:
  name: single-provider  ## 应用程序名称，后面会在消费者中用到
```
1.2.3源码编写
（a）启动类
```java
@EnableEurekaClient//表示要注册到注册中心
@SpringBootApplication
public class SingleProviderApplication {
   public static void main(String[] args) {
       SpringApplication.run(SingleProviderApplication.class, args);
  }
}
```

（b）其他
```java

```

1.3服务消费者
1.3.1导包
```
<dependency>
   <groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
   <groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

1.3.2配置文件
（a）bootstrap.yml
```

```
（b）application.yml
```
<dependency>
   <groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
   <groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```
1.3.3源码编写
（a）启动类
```java
@SpringBootApplication
@EnableEurekaClient//声明此项目为一个 eureka 客户端
@EnableFeignClients//声明此项目可以使用 Feign
public class SingleCustomerApplication {
   /**
    * 注入 RestTemplate
    * 并用 @LoadBalanced 注解，用负载均衡策略请求服务提供者
    * 这是 Spring Ribbon 的提供的能力
    * @return
    */
   @LoadBalanced
   @Bean
   public RestTemplate restTemplate() {
       return new RestTemplate();
  }
   public static void main(String[] args) {
       SpringApplication.run(SingleCustomerApplication.class, args);
  }
}
```
（b）其他  
```java
/**
* IHelloService
* 配置服务提供者：single-provider 是服务提供者的 application.name
*/
@FeignClient("single-provider")//@FeignClient 注解的 value 为服务提供者的 appplication.name 
public interface IHelloService {
   @RequestMapping(value = "/hello")
   String hello();
   @RequestMapping(value = "nice")
   String nice();
}
```  


参考：  
https://mp.weixin.qq.com/s?__biz=MzAxMjA0MDk2OA==&mid=2449468935&idx=1&sn=1069a1b749ad9faf4958c5c091a08fa8&chksm=8fbca060b8cb2976b8c679db88d76d80c1d69cafa58d8ec7a7d32abbe9228b41b666f928e1c2&scene=178#rd