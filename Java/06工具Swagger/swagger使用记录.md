swagger是一个生成规范的接口文档的工具

1.spring boot项目集成
引入依赖jar包
```xml
<!-- swagger基础包 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.2.2</version>
</dependency>
<!-- swagger UI强化包 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.2.2</version>
</dependency>

```  

创建配置类（没有做好配置会导致部分接口无法被映射到ui界面，然后就找不到这部分耳接口了）  
```java
@Configuration
@EnableSwagger2
public class Swagger2Config {
    //swagger：/trans/**  接口映射
    @Bean
    public Docket createRestTrans() {
        ParameterBuilder ticketPar = new ParameterBuilder();
        List<Parameter> pars = new ArrayList<>();
        ticketPar.modelRef(new ModelRef("string")).parameterType("header")
                .required(false).build();
        ticketPar.name("Authorization").description("user token")
                .modelRef(new ModelRef("string")).parameterType("header")
                .required(false).build();
        pars.add(ticketPar.build());

        return new Docket(DocumentationType.SWAGGER_2)
                .groupName("trans")
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.hikvision.hikkan.yres"))
                .paths(PathSelectors.ant("/trans/**"))
                .build()
                .globalOperationParameters(pars);
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("yres")
                .version("1.0")
                .build();
    }

}

```


常用注解  
```java
Api：修饰整个类，描述Controller的作用
ApiOperation：描述一个类的一个方法，或者说一个接口
ApiParam：单个参数描述
ApiModel：用对象来接收参数
ApiProperty：用对象接收参数时，描述对象的一个字段
ApiResponse：HTTP响应其中1个描述
ApiResponses：HTTP响应整体描述
ApiIgnore：使用该注解忽略这个API
ApiError ：发生错误返回的信息
ApiImplicitParam：一个请求参数
ApiImplicitParams：多个请求参数
```

@NotNull  
不能为null，但是可以为空
@NotEmpty  
不能weinull，也不可以为空
@NotBlank  
只能用于接收String，且字符串trim()之后不可以为0

注：trim()方法去除字符串头尾的空格

参考：  
1.http://blog.itpub.net/69957347/viewspace-2673042/（基本使用）  
2.https://www.jianshu.com/p/d0a30ad5b424（配置类的作用）