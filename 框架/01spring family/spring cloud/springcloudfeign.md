客户端服务调用服务端接口的方法有两种：结合ribbon与[resttemplate](../spring/resttemplate.md)，openfeign。   
[访问服务接口](../../../image/spring/springcloud/两种服务端接口调用方式.png)  

openfeign主要用来解决一个依赖服务接口会在多处被调用，为了减少开发工作量，针对各个微服务自行封装一些客户端累来包装这些依赖服务的调用。


使用demo  
```java
@SpringBootApplication
@EnableFeignClients//开启openfeign支持
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```  
服务客户端的使用  
```java
@FeignClient("stores")//服务端的名称
public interface StoreClient {
    @RequestMapping(method = RequestMethod.GET, value = "/stores")
    List<Store> getStores();

    @RequestMapping(method = RequestMethod.GET, value = "/stores")
    Page<Store> getStores(Pageable pageable);

    @RequestMapping(method = RequestMethod.POST, value = "/stores/{storeId}", consumes = "application/json")
    Store update(@PathVariable("storeId") Long storeId, Store store);
}
```





参考：  
1.https://www.cnblogs.com/codecow/p/12714680.html










