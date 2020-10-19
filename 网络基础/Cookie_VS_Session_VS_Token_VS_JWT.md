1.认证VS授权  
=  
认证：目的是验证当前用户的身份（常见的登录过程其实就是在验证用户是否本人）  
授权：用户授予第三方访问某些资源的权限  

凭证：认证和授权都需要一种媒介（证书）来标记访问者的身份（身份证）  

2.Cookie  
=  
2.1为什么需要cookie  
-  
由于http是无状态的协议，服务器不知道每次发来请求的是否是同一个人，每个请求都是完全独立的，为了知道是谁在请求服务，可以维护一个状态来告知服务器访问者的信息，这个状态就可以用cookie实现。  

2.2初识cookie  
-  
### 2.2.1工作流程  
cookie是服务器产生的，存在在客户端的一块数据，会在浏览器下一次请求统一个服务器的时候自动带上这块数据  

### 2.2.2重要属性  
| 属性 | 描述 |
| :----: | :----: |
| name=value | 键值对，设置 Cookie 的名称及相对应的值，都必须是字符串类型- 如果值为 Unicode 字符，需要为字符编码。- 如果值为二进制数据，则需要使用 BASE64 编码。 |
|domain|指定 cookie 所属域名，默认是当前域名（每个 cookie 都会绑定单一的域名，无法在别的域名下获取使用，一级域名和二级域名之间是允许共享使用的）|
|path|指定 cookie 在哪个路径（路由）下生效，默认是 '/'。如果设置为 /abc，则只有 /abc 下的路由可以访问到该 cookie，如：/abc/read。|
|maxAge|cookie 失效的时间，单位秒。如果为整数，则该 cookie 在 maxAge 秒后失效。如果为负数，该 cookie 为临时 cookie ，关闭浏览器即失效，浏览器也不会以任何形式保存该 cookie 。如果为 0，表示删除该 cookie 。默认为 -1。- 比 expires 好用。|
|expires|过期时间，在设置的某个时间点后该 cookie 就会失效。一般浏览器的 cookie 都是默认储存的，当关闭浏览器结束这个会话的时候，这个 cookie 也就会被删除|
|secure|该 cookie 是否仅被使用安全协议传输。安全协议有 HTTPS，SSL等，在网络上传输数据之前先将数据加密。默认为false。当 secure 值为 true 时，cookie 在 HTTP 中是无效，在 HTTPS 中才有效。|
|httpOnly|httpOnly如果给某个 cookie 设置了 httpOnly 属性，则无法通过 JS 脚本 读取到该 cookie 的信息，但还是能通过 Application 中手动修改 cookie，所以只是在一定程度上可以防止 XSS 攻击，不是绝对的安全|  

3.Session
=  
基于cookie实现的一种记录客户端与服务器的会话的机制  
3.1为什么需要session
-  
更安全，session保存在服务器上；  
存取值得类型更丰富，cookie只能保存字符串，session可以是任意类型；  
有效期较短，cookie可以一直存活，session默认关闭浏览器或者session超时后失效；  
存储量跟大，cookie可以保存不超过4K数据，session理论上这个值远高于cookie，但是太大会占用服务器太多的资源


3.2初识session
-  
### 3.2.1工作流程
客户端第一次访问服务器的时候就会生成session，保存在服务器上，返回这个session的唯一标识符sessionid给客户端（客户端将其保存在cookie中）。下一次访问的时候，就把这个sessionid发送给服务器，服务器根据id查询session，判断是否存在或者过期。  

4.Token令牌
=  
访问资源接口（API）时所需要的资源凭证  
4.1为什么需要token
-  
token是无状态的认证方式（意味着更容易扩展，不同服务器只要知道怎么解析就可以），服务器不用保存类似session的数据，减轻了存取数据库过程的压力（或者内存压力），代价是增加验证过程的计算成本；  
更安全， SessionID要严格保密，被别人火球到了，他就有了全部权限；token则可以做更多控制，包含了认证和授权，授权可以让第三方只有指定的访问资源的权限。


4.2初识Token   
-  
### 4.2.1工作流程  
客户端用用户名和密码去请求服务器，服务器处理这个请求（验证，加密数据等操作），然后签发一个token返回给客户端；客户端存储token，以后请求的时候带着这个token，服务器收到请求后，验证这个token数据，验证通过后再处理请求返回结果。

### 4.2.2Acesss Token和Refresh Token  
Acesss Token：  
Refresh Token（保存在数据库中）：专门用户刷新Acesss Token，（只有在刷新Acesss Token的时候才会去验证，不会影响业务接口响应时间）Refresh Token未过期，就直接返回一个新Acesss Token；反之，就只能重新登录了。


5.JWT（JSON Web Token）  
=  
是目前最流行的跨域认证解决方案  
5.1为什么需要JWT  
-  


5.2初识JWT  
-  
### 5.2.1原理  
服务器认证以后，生成一个 JSON 对象，发回给用户，以后再访问的时候，带上这个JSON对象就可以  


### 5.2.2构成  
#### 5.2.2.1Header（头部）  
Header 部分是一个 JSON 对象，使用 Base64URL 算法转成字符串。  
```
{
  "alg": "HS256",
  "typ": "JWT"
}
```
>alg属性表示签名的算法（algorithm），默认是 HMAC SHA256（写成 HS256）；  
>typ属性表示这个令牌（token）的类型（type），JWT 令牌统一写为JWT;

#### 5.2.2.2Payload（负载）  
Payload 部分也是一个 JSON 对象，用来存放实际需要传递的数据，使用 Base64URL 算法转成字符串。
```
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```
这里会涉及几个官方字段，需要注意一下，  
| 字段 | 描述 |
| :----: | :----: |
|iss (issuer)|签发人|
|exp (expiration time)|过期时间|
|sub (subject)|主题|
|aud (audience)|受众|
|nbf (Not Before)|生效时间|
|iat (Issued At)|签发时间|
|jti (JWT ID)|编号|

#### 5.2.2.3Signature（签名）  
Signature 部分是对前两部分的签名，防止数据篡改。（需要指定一个密钥“secret”，这个密钥只有服务器才知道，不能泄露给用户。然后，使用 Header 里面指定的签名算法（默认是 HMAC SHA256），按照下面的公式产生签名。）
>HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)  

算出签名以后，把 Header、Payload、Signature 三个部分拼成一个字符串，每个部分之间用"点"（.）分隔  
>Header.Payload.Signature  

一个栗子：  
>eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJBdXRob3JpemF0aW9uIiwicGF5bG9hZCI6IntcImV4cGlyZWRcIjozMCxcIm5pY2tOYW1lXCI6XCLotoXnuqfnrqHnkIblkZhcIixcInByb2R1Y3RDb2RlXCI6XCJcIixcInNob3dQcml2YWN5QWd0XCI6ZmFsc2UsXCJzaG93VHJpYWxBZ3RcIjpmYWxzZSxcInRpbWVcIjpcIjE2MDI2NTUwNDQ1OTJcIixcInR5cGVcIjoyLFwidXNlcklkXCI6MSxcInVzZXJOYW1lXCI6XCJhZG1pblwiLFwidXNlclR5cGVcIjowfSJ9.cSWMhG3z6WAL61ZUQFyAv-XmqHqxbmIlmhWwXJfBx9UUHPs6sZkrBKdANjjbjlt309nhCw2sgu8wafnoVsALug  




















参考：  
1.https://juejin.im/post/6844904034181070861  
2.https://www.jianshu.com/p/576dbf44b2ae  
3.http://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html
