HashiCorp 公司推出，用于提供服务发现和服务配置的工具
* 服务发现 让服务注册和服务发现（通过 DNS 和 HTTP 接口）更加简单，甚至对于外部服务（例如SaaS）注册也一样。  
* 故障检测 通过健康检查，服务发现可以防止请求被路由到不健康的主机，并且可以使服务容易断开（不再提供服务）  
* 多数据中心 Consul 不需要复杂的配置即可简便的扩展到多个数据中心，查找其它数据中心的服务或者只请求当前数据中心的服务。  
* 键值存储 灵活的键值存储，提供动态配置、特征标记、协作、leader 选举等功能，通过长轮询实现配置改变的即时通知。

Spring Cloud Consul 可替代已有的 Spring Cloud Eureka, Spring Cloud Config  

与Eureka可以直接使用不同，Consul需要进行单独的安装。





参考：  
1.https://blog.csdn.net/a312586670/article/details/105337943/


























