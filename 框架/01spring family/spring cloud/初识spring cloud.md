spring cloud  
我们自己去构建一微服务的过程中，需要做如服务发现注册、配置中心、消息总线、负载均衡、断路器、数据监控等操作，spring cloud为我们提供了一套简易的编程模型，使我们能在 Spring Boot 的基础上轻松地实现微服务项目的构建。  
![整体结构](../../../image\spring\springcloud\springcloud整体结构.png)

1.Spring Cloud 的服务发现框架——Eureka  
服务发现：类比实际生活中的案例，就是租客（Consumer）、中介（Eureka）与房东（Provider），涉及三个角色：  
![服务发现的理解](../../../image\spring\springcloud\服务发现的理解.png)
* 服务提供者：就是提供一些自己能够执行的一些服务给外界。  
* 服务消费者：就是需要使用一些服务的“用户”。  
* 服务中介： 其实就是服务提供者和服务消费者之间的“桥梁”，服务提供者可以把自己注册到服务中介那里，而服务消费者如需要消费一些服务(使用一些功能)就可以在服务中介中寻找注册在服务中介的服务提供者。

服务提供者（房东）会进行服务注册（给中介房子的信息）、服务续约操作（告诉中介房子一直处于可以出租的状态）、服务下线（告诉中介房子不出租了）
* 服务注册 Register：当 Eureka 客户端向 Eureka Server 注册时，它提供自身的元数据，比如IP地址、端口，运行状况指示符URL，主页等。  
* 服务续约 Renew：Eureka 客户会每隔30秒(默认情况下)发送一次心跳来续约。 通过续约来告知 Eureka Server 该 Eureka 客户仍然存在，没有出现问题。 正常情况下，如果 Eureka Server在90秒没有收到 Eureka 客户的续约，它会将实例从其注册表中删除。  
* 服务下线 Cancel：Eureka客户端在程序关闭时向Eureka服务器发送取消请求。 发送请求后，该客户端实例信息将从服务器的实例注册表中删除。该下线请求不会自动完成，它需要调用以下内容：DiscoveryManager.getInstance().shutdownComponent();

Eureka服务器（中介）会进行服务剔除（把那些不租的房子从自己的房源信息里面删除）  
服务剔除 Eviction：在默认的情况下，当Eureka客户端连续90秒(3个续约周期)没有向Eureka服务器发送服务续约，即心跳，Eureka服务器会将该服务实例从服务注册列表删除，即服务剔除。  

服务消费者（租客）会去获取注册列表信息（向中介要房子的列表）
* 获取注册列表信息 Fetch Registries：Eureka 客户端从服务器获取注册表信息，并将其缓存在本地。客户端会使用该信息查找其他服务，从而进行远程调用。该注册列表信息定期（每30秒钟）更新一次。每次返回注册列表信息可能与 Eureka 客户端的缓存信息不同, Eureka 客户端自动处理。如果由于某种原因导致注册列表信息不能及时匹配，Eureka 客户端则会重新获取整个注册表信息。 Eureka 服务器缓存注册列表信息，整个注册表以及每个应用程序的信息进行了压缩，压缩内容和没有压缩的内容完全相同。Eureka 客户端和 Eureka 服务器可以使用JSON / XML格式进行通讯。在默认的情况下 Eureka 客户端使用压缩 JSON 格式来获取注册列表的信息。  

![服务发现官方理解](../../../image\spring\springcloud\服务发现官方理解.png)

RestTemplate是Spring提供的一个访问Http服务的客户端类，微服务之间的调用是使用的 RestTemplate。\[延伸学习\]


2.负载均衡
换个栗子进行说明:秒杀系统的栗子。  
为了应对大量的客户产生的大量请求，我们会搞一个服务器的集群，用来一起为客户提供服务。但是，如果请求过程把请求基本都发到一台服务器上，其他的都是摆设，那一台服务器压力山大啊。这时候，就需要有请负载均衡器隆重登场了，利用负载均衡算法，把压力合理的分配给各个服务器（有难同当）。  

这时候，我们就有两个比较熟悉的选择了（Nginx和Ribbon）\[延伸学习\]  
就和git和svn一样，出现了集中式和分布式两种选择，不知道理解是否正确\[延伸学习\]  

Nginx把请求先集中在一起，然后分发下去进行负载均衡；  
Ribbon则是在消费者那里就依据获取的注册列表信息进行负载均衡；
![nginx](../../../image\spring\springcloud\nginx.png)
![ribbon](../../../image\spring\springcloud\ribbon.png)  

几种负载均衡算法：
Nginx：  
轮询和加权轮询算法。

Ribbon：  
RoundRobinRule：轮询策略。Ribbon 默认采用的策略。若经过一轮轮询没有找到可用的 provider，其最多轮询 10 轮。若最终还没有找到，则返回 null。  
RandomRule: 随机策略，从所有可用的 provider 中随机选择一个。  
RetryRule: 重试策略。先按照 RoundRobinRule 策略获取 provider，若获取失败，则在指定的时限内重试。默认的时限为 500 毫秒。  


Eureka告诉我们有哪些服务提供者
Ribbon告诉我们去找那个服务提供者最好
RestTemplate是我们之间的沟通方式的实现
 有了上面三个东西，我们已经可以实现服务间的调用了，但是每次要去用RestTemplate调用服务的时候，都要自己写一遍url，request，返回类型，不是很方便。  

 OpenFeign粉墨登场，将被调用的服务代码映射到消费者端，OpenFeign也是运行在消费者端的，使用 Ribbon进行负载均衡，直接内置了 Ribbon。
 ```java
// 使用 @FeignClient 注解来指定提供者的名字
@FeignClient(value = "eureka-client-provider")
public interface TestClient {
    // 这里一定要注意需要使用的是提供者那端的请求相对路径，这里就相当于映射了
    @RequestMapping(value = "/provider/xxx",
    method = RequestMethod.POST)
    CommonResponse<List<Plan>> getPlans(@RequestBody planGetRequest request);
}

@RestController
public class TestController {
    // 这里就相当于原来自动注入的 Service
    @Autowired
    private TestClient testClient;
    // controller 调用 service 层代码
    @RequestMapping(value = "/test", method = RequestMethod.POST)
    public CommonResponse<List<Plan>> get(@RequestBody planGetRequest request) {
        return testClient.getPlans(request);
    }
}
 ```


3.Hystrix  
由于各个微服务之间存在着调用关系，会形成一个调用链，正常工作还行，没什么大问题，但是，如果其中的一环太忙了，忙不过来了，回导致调用这一环的上一环一直等着，请求开始积攒起来，最后也忙不过来，有影响到再上一层的，一直这么下去，就不得了了，整个系统都会崩的（雪崩）  

Hystrix通过隔离服务之间的访问点，停止服务之间的级联故障并提供后备选项来实现此目的，所有这些都可以提高系统的整体弹性。

简单的说就是，Hystrix看见有一个环节的服务忙不过来了，就告诉上一个环节，你可以不用等了，他忙不过来了，你就返回处理不了或者采取其他措施就完了。

熔断：当指定时间窗内的请求失败率达到设定阈值时，系统将通过 断路器 直接将此请求链路断开。  
@HystrixCommand 注解来标注某个方法，这样 Hystrix 就会使用 断路器 来“包装”这个方法，每当调用时间超过指定时间时(默认为1000ms)，断路器将会中断对这个方法的调用。  

降级：是为了更好的用户体验，当一个方法调用异常时，通过执行另一种代码逻辑来给用户友好的回复。
```java
// 指定了后备方法调用
@HystrixCommand(fallbackMethod = "getHystrixNews")
@GetMapping("/get/news")
public News getNews(@PathVariable("id") int id) {
    // 调用新闻系统的获取新闻api 代码逻辑省略
}
// 
public News getHystrixNews(@PathVariable("id") int id) {
    // 做服务降级
    // 返回当前人数太多，请稍后查看
}
```



4.微服务网关——Zuul  
上面我们知道了服务消费者、服务中介和服务提供者的存在，这里又出来一个用户，用户会去调用那些服务消费者工程，我们当然可以一对一的去访问，但是，有那么多的消费者工程，用起来就很不方便，怎么办？  

热心肠的zuul就是一个对于 消费者 的统一入口。
![zuul](../../../image\spring\springcloud\zuul.png)   
![zuul官方](../../../image\spring\springcloud\zuul官方.png)  

有了zuul，就可以对用户对消费者的访问进行更好的管理与访问了  
路由映射、统一前缀、路由策略配置、服务名屏蔽、路径屏蔽、敏感请求头屏蔽、Zuul 的过滤功能、限流、权限校验等等


5.Spring Cloud配置管理——Config
我们的微服务体系里面，每一个项目都有自己的配置文件，服务消费者，中介，提供者，zuul都有对应的配置文件，在项目运行的过程中，如果我们不进行配置的统一管理，我们只能去每个应用下一个一个寻找配置文件然后修改配置文件再重启应用。

首先，对于分布式系统而言我们就不应该去每个应用下去分别修改配置文件；其次，重启应用，服务就无法访问，直接抛弃了可用性，这肯定不可以。

Spring Cloud Config 为分布式系统中的外部化配置提供服务器和客户端支持。使用 Config 服务器，可以在中心位置管理所有环境中应用程序的外部属性。（直白一点就是：Spring Cloud Config 就是能将各个 应用/系统/模块 的配置文件存放到 统一的地方然后进行管理(Git 或者 SVN)。）

这里有个问题就是，我们改了远程仓库里面的配置文件，赖于这个配置文件的已启动的应用不会进行其相应配置的更改。于是，下一个大佬又大步向我们走来（好慌啊，又是一个大佬）  

6.Spring Cloud Bus  
管理和广播分布式系统中的消息




参考：  
https://zhuanlan.zhihu.com/p/95696180?from_voters_page=true