为什么需要spring cloud config配置中心  
在使用配置中心之前：  
微服务架构各个项目的配置文件分散在各个项目里，不方便维护；  
配置内容安全与权限，通过一个项目的不同版本的配置文件，可以分离开发、测试、正式生产环境的配置文件；  
更新配置后，项目需要重启；  

1.最简单的配置中心  
最简单的配置中心就是启动一个服务作为服务方，需要获取配置文件的其余项目作为客户端获取这个服务方的服务  

1.1首先，需要在远程仓库创建一个对应的配置文件存放仓库，通常是github。  
仓库里面的配置文件命名当然不能是随意的，需要满足一定的规则，需要对应Spring Cloud Config的访问规则  
```
/{application}/{profile}[/{label}]
/{application}-{profile}.yml
/{label}/{application}-{profile}.yml
/{application}-{profile}.properties
/{label}/{application}-{profile}.properties
```  
{application} 就是应用名称，对应到配置文件上来，就是配置文件的名称部分；  
{profile} 就是配置文件的版本，我们的项目有开发版本、测试环境版本、生产环境版本，对应到配置文件上来就是以 {application}-{profile}.yml 加以区分，例如application-dev.yml、application-sit.yml、application-prod.yml；  
{label} 表示 git 分支，默认是 master 分支，如果项目是以分支做区分也是可以的，那就可以通过不同的 label 来控制访问不同的配置文件了。  

需要注意的是：
（1）如果有两个前缀名相同文件，例如一个order.yml，一个order-dev.yml。那么在访问相同前缀的文件时，config-server会对这两个文件进行一个合并。例如order.yml有一段配置是order-dev.yml没有的，理应访问order-dev.yml的时候是没有那段配置的，但访问的结果却是它俩合并之后的内容，即order-dev.yml会拥有order.yml里所配置的内容。  
（2）随着后期微服务数量的增加，配置文件的数量自然也会随着增加，而且实际的企业项目中都会在不同的部署环境使用不同的配置文件，例如开发环境（dev）、测试环境（test）、生产环境（product）等。所以一个服务至少会有三个以上的配置文件，如果我们将这些配置文件直接放在git仓库的根目录下的话，就会显得很杂乱，不便于查看、修改，很自然的会想到将不同服务的配置文件放到以服务名命名的目录下，但是当你开开心心的将配置文件整理到一个个的目录里并重启了config server后，就会发现这些配置文件全都加载不到了。这是因为config server默认情况下只会搜索git仓库根路径下的配置文件，所以我们还需要加上一个配置项：search-paths，该配置项用于指定config server搜索哪些路径下的配置文件，需要注意的是这个路径是相对于git仓库的，并非是项目的路径。

1.2创建服务方项目  
1.2.1添加依赖  
```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- spring cloud config 服务端包 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
```
1.2.2配置文件  
bootstrap.yml   
```
spring:
  application:
    name: config-single-server  # 应用名称
  cloud:
     config:
        server:
          git:
            uri: https://github.com/... #配置文件所在仓库
            username: github 登录账号
            password: github 登录密码
            default-label: master #配置文件分支
            search-paths: config  #配置文件所在根目录
```  
application.yml  
```
server:
  port: 3301
```
1.2.3启动类  
```java
@SpringBootApplication
@EnableConfigServer
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```


启动spring boot项目，按照访问规则，访问如下的地址，就可以访问到远程仓库中的配置文件
```
http://localhost:3301/config-single-client/dev/master
http://localhost:3301/config-single-client/prod
http://localhost:3301/config-single-client-dev.yml
http://localhost:3301/config-single-client-prod.yml
http://localhost:3301/master/config-single-client-prod.yml  
```  
其实，如果访问.properties格式的，还会自动进行转换，.json格式的也能够进行转换。（如果配置文件的内容格式有问题的话，访问会报500错误。我们可以利用这个特性，来检查我们的配置文件是否正确）


1.3客户端
1.3.1添加依赖  
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- spring cloud config 客户端包 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```
1.3.2配置文件  
bootstrap.yml
```
spring:
  profiles:
    active: dev #配置了两个版本的配置，并通过 spring.profiles.active 设置当前使用的版本
---
spring:
  profiles: prod
  application:
    name: config-single-client
  cloud:
     config:
       uri: http://localhost:3301
       label: master
       profile: prod
---
spring:
  profiles: dev
  application:
    name: config-single-client
  cloud:
     config:
       uri: http://localhost:3301
       label: master
       profile: dev
```  
application.yml  
```
server:
  port: 3302
management:  #关于actuator相关的，自动刷新配置的时候需要使用
  endpoint:
    shutdown:
      enabled: false
  endpoints:
    web:
      exposure:
        include: "*"

data: #data是当无法读取配置中心的配置时，使用此配置，以免项目无法启动。
  env: NaN
  user:
    username: NaN
    password: NaN
```  
1.3.3启动类与读取配置属性的类  
```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```  

```java
@Data
@Component
public class GitConfig {
    @Value("${data.env}")// 由于@Value 注解的实现原理的限制，refresh 机制无法刷新这种配置属性的读取结果，不建议使用这种方式
    private String env;
    @Value("${data.user.username}")
    private String username;
    @Value("${data.user.password}")
    private String password;
}
```

```java
@Component
@Data
@ConfigurationProperties(prefix = "data")
public class GitAutoRefreshConfig {

    public static class UserInfo {
        private String username;
        private String password;
        public String getUsername() {
            return username;
        }
        public void setUsername(String username) {
            this.username = username;
        }
        public String getPassword() {
            return password;
        }
        public void setPassword(String password) {
            this.password = password;
        }
        @Override
        public String toString() {
            return "UserInfo{" +
                    "username='" + username + '\'' +
                    ", password='" + password + '\'' +
                    '}';
        }
    }
    private String env;
    private UserInfo user;
}
```

```java  
@RestController
@RefreshScope //@RefreshScope 注解并结合 actuator,需要我们主动触发更新配置文件，发送 POST 请求到 http://localhost:3302/actuator/refresh 这个接口
public class GitController {

    @Autowired
    private GitConfig gitConfig;
    @Autowired
    private GitAutoRefreshConfig gitAutoRefreshConfig;
    @GetMapping(value = "show")
    public Object show(){
        return gitConfig;
    }
    @GetMapping(value = "autoShow")
    public Object autoShow(){
        return gitAutoRefreshConfig;
    }
}
```  

从上面的配置文件刷新过程来看，每次配置文件更新后，我们都需要去发送一个post请求到http://localhost:3302/actuator/refresh接口，使得更新生效，但是这样有点麻烦啊，所有，github为我们提供了一个解决办法Webhook，当有代码变更的时候，会调用我们设置的地址（就是那个actuator/refresh接口了），来实现我们想达到的目的。


但是，这样对于一个项目还好，要是是一堆的集群服务呢，总不能一个一个去搞吧，还是有可以偷懒的地方（偷懒是推动进步的一个重要动力）。  

[Spring Cloud Bus](./springcloudbus.md)闪亮登场
1.4Spring Cloud Bus配合更新配置文件  
1.4.1添加依赖  
前提：有消息中间件环境支持[RabbitMQ](../../../中间件\消息队列\RabbitMQ\RabbitMQ.md)/[kafka](../../../中间件\消息队列\kafka\kafka.md)  
```  
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>
```
1.4.2配置rabbitmq
```
spring:
  rabbitmq:
    host: localhost
    port: 32775
    username: guest
    password: guest
```
1.4.3POST访问一个客户端的actuator/bus-refresh地址

2结合[Eureka](./springcloudeureka.md)使用Spring Cloud Config  
2.1Eureka Server  

2.2服务端注册到Eureka

2.3客户端注册到Eureka，通过Eureka来访问服务端



参考：  
https://www.cnblogs.com/fengzheng/p/11242128.html

