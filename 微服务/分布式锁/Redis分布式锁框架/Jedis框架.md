1.概念  
=  
Jedis是Redis官方推荐的Java连接开发工具。  

2.基础使用示例  
=  
2.1利用Jedis实现Redis分布式锁  
-
### 2.1.1添加组件依赖  
```
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>2.9.0</version>
</dependency>
```  
### 2.1.2代码实现  
正确姿势  
```java
public class RedisTool {
 
    private static final String LOCK_SUCCESS = "OK";
    private static final String SET_IF_NOT_EXIST = "NX";
    private static final String SET_WITH_EXPIRE_TIME = "PX";
    private static final Long RELEASE_SUCCESS = 1L; 
    /**
     * 尝试获取分布式锁
     * @param jedis Redis客户端
     * @param lockKey 锁
     * @param requestId 请求标识
     * @param expireTime 超期时间
     * @return 是否获取成功
     */
    public static boolean tryGetDistributedLock(Jedis jedis, String lockKey, String requestId, int expireTime) {
        String result = jedis.set(lockKey, requestId, SET_IF_NOT_EXIST, SET_WITH_EXPIRE_TIME, expireTime);
        if (LOCK_SUCCESS.equals(result)) {
            return true;
        }
        return false;
 
    }

    /**
     * 释放分布式锁
     * @param jedis Redis客户端
     * @param lockKey 锁
     * @param requestId 请求标识
     * @return 是否释放成功
     */
    public static boolean releaseDistributedLock(Jedis jedis, String lockKey, String requestId) {
        String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
        Object result = jedis.eval(script, Collections.singletonList(lockKey), Collections.singletonList(requestId));
        if (RELEASE_SUCCESS.equals(result)) {
            return true;
        }
        return false;
    }
}

```

>代码说明  
>加锁函数的参数说明：  
>@param key   缓存键  
>@param value 缓存值  
>@param nxxx  该的值只能取 NX 或者 XX  
>             如果取 NX，则只有当 key 不存在时才进行 set  
>             如果取 XX，则只有当 key 已经存在时才进行 set  
>@param expx  该的值只能取 EX 或者 PX，代表数据过期时间的单位，EX 代表秒，PX 代表毫秒  
>@param time  过期时间，单位是 expx 所代表的单位  
>
>解锁函数的参数说明：  
>script写了一个简单的Lua脚本代码：首先获取锁对应的value值，检查是否与requestId相等，如果相等则删除锁（解锁）。  
>jedis.eval将Lua代码交给Redis服务端执行：在eval命令执行Lua代码的时候，Lua代码将被当成一个命令去执行，并且直到eval命令执行完成，Redis才会执行其他命令（原子性）  


错误姿势
容易混淆，就不写了，参考链接1中内容  


3.接口文档  
=  
[Jedis官方接口文档](https://tool.oschina.net/uploads/apidocs/overview-summary.html)  
[Jedis-JedisCommands接口中各方法的详细注释](https://blog.csdn.net/qq_35246620/article/details/105029412)


































参考：  
1.https://blog.csdn.net/lzx1991610/article/details/108200851