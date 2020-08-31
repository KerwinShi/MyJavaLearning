以前的开发过程中，经常需要写一些字段校验的代码（字段非空，字段长度限制，格式验证等等），存在如下的问题：
* 验证代码繁琐，冗余
* 参数验证过程还要去阅读源码进行验证  

为了简化开发，引入一套参数校验实现方式，统一处理。

Spring Validator和Hibernate Validator就是两套常用的Validator
1.hibernate validator  
- 
spring-boot-starter-web包里面有hibernate-validator包，不需要引用hibernate validator依赖。  
```java
//demo
//接受实体类
@Getter
@Setter
@NoArgsConstructor
public class DemoModel {
    @NotBlank(message="用户名不能为空")
    private String userName;

    @NotBlank(message="年龄不能为空")
    @Pattern(regexp="^[0-9]{1,2}$",message="年龄不正确")
    private String age;

    @AssertFalse(message = "必须为false")
    private Boolean isFalse;
    /**
     * 如果是空，则不校验，如果不为空，则校验
     */
    @Pattern(regexp="^[0-9]{4}-[0-9]{2}-[0-9]{2}$",message="出生日期格式不正确")
    private String birthday;
}


//Controller使用  
//@Valid表明需要进行参数校验，BindindResult用来接收校验的结果（对于如果传入多个多个参数，可以加多个@Valid和BindindResult的组合）
@RequestMapping("/demo2")
public void demo2(@RequestBody @Valid DemoModel demo, BindingResult result){
    if(result.hasErrors()){
        for (ObjectError error : result.getAllErrors()) {
            System.out.println(error.getDefaultMessage());
        }
    }
}
```  
输入参数  
```
{"userName":"dd","age":120,"isFalse":true,"birthday":"21010-21-12"}
```
输出结果  
```
出生日期格式不正确
必须为false
年龄不正确
```

hibernate的校验模式有两种，上面的demo里面用的是默认的普通模式（对所有参数进行验证，一次返回所有不符合要求的结果），另外一种就是快速失败返回的模式（只要找到有一个参数不符合校验的要求，立刻结束校验过程，返回失败结果）  
模式设置方法：
```java
//第一种
ValidatorFactory validatorFactory = Validation.byProvider( HibernateValidator.class )
        .configure()
        .failFast( true )
        .buildValidatorFactory();
Validator validator = validatorFactory.getValidator();
//第二种
ValidatorFactory validatorFactory = Validation.byProvider( HibernateValidator.class )
        .configure()
        .addProperty( "hibernate.validator.fail_fast", "true" )
        .buildValidatorFactory();
Validator validator = validatorFactory.getValidator();
```
项目中的使用方法：  
```java
//配置类
@Configuration
public class ValidatorConfiguration {
    @Bean
    public Validator validator(){
        ValidatorFactory validatorFactory = Validation.byProvider( HibernateValidator.class )
                .configure()
                .addProperty( "hibernate.validator.fail_fast", "true" )
                .buildValidatorFactory();
        Validator validator = validatorFactory.getValidator();

        return validator;
    }
}
```  

请求参数校验在项目中的使用  
* 请求参数校验  
    在处理GET请求或者参数比较少的时候，会使用`public void demo3(@RequestParam(name = "grade", required = true) int grade,@RequestParam(name = "classroom", required = true) int classroom)`这样的方式进行校验。  
* GET参数校验  
    这时候如果像demo里那样使用@valid是没有用的，需要使用@validated(使用三部曲)：  
    1.相关配置  
```java
@Configuration
public class ValidatorConfiguration {
    @Bean
    public MethodValidationPostProcessor methodValidationPostProcessor() {
        MethodValidationPostProcessor postProcessor = new MethodValidationPostProcessor();　　　　　
        /**设置validator模式为快速失败返回*/
        postProcessor.setValidator(validator());
        return postProcessor;
    }

    @Bean
    public Validator validator(){
        ValidatorFactory validatorFactory = Validation.byProvider( HibernateValidator.class )
                .configure()
                .addProperty( "hibernate.validator.fail_fast", "true" )
                .buildValidatorFactory();
        Validator validator = validatorFactory.getValidator();

        return validator;
    }
}
```  
    2.Controller注解与使用  
```java
@RequestMapping("/validation")
@RestController
@Validated
public class ValidationController {
    /**如果只有少数对象，直接把参数写到Controller层，然后在Controller层进行验证就可以了。*/
    @RequestMapping(value = "/demo3", method = RequestMethod.GET)
    public void demo3(@Range(min = 1, max = 9, message = "年级只能从1-9")
                      @RequestParam(name = "grade", required = true)
                      int grade,
                      @Min(value = 1, message = "班级最小只能1")
                      @Max(value = 99, message = "班级最大只能99")
                      @RequestParam(name = "classroom", required = true)
                      int classroom) {
        System.out.println(grade + "," + classroom);
    }
}
```  
    3.异常返回统一处理  
```java  
//验证不通过会抛出ConstraintViolationException异常，进行统一异常捕获处理
@ControllerAdvice
@Component
public class GlobalExceptionHandler {

    @ExceptionHandler
    @ResponseBody
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public String handle(ValidationException exception) {
        if(exception instanceof ConstraintViolationException){
            ConstraintViolationException exs = (ConstraintViolationException) exception;

            Set<ConstraintViolation<?>> violations = exs.getConstraintViolations();
            for (ConstraintViolation<?> item : violations) {　　　　　　　　　　
                /**打印验证不通过的信息*/
                System.out.println(item.getMessage());
            }
        }
        return "bad request, " ;
    }
}

```  

* model校验  
```java  
//model定义
@Data
public class Demo2 {
    @Length(min = 5, max = 17, message = "length长度在[5,17]之间")
    private String length;

    /**@Size不能验证Integer，适用于String, Collection, Map and arrays*/
    @Size(min = 1, max = 3, message = "size在[1,3]之间")
    private String age;

    @Range(min = 150, max = 250, message = "range在[150,250]之间")
    private int high;

    @Size(min = 3,max = 5,message = "list的Size在[3,5]")
    private List<String> list;
}

//使用
@Autowired
private Validator validator;
@RequestMapping("/demo3")
public void demo3(){
    Demo2 demo2 = new Demo2();
    demo2.setAge("111");
    demo2.setHigh(150);
    demo2.setLength("ABCDE");
    demo2.setList(new ArrayList<String>(){{add("111");add("222");add("333");}});
    Set<ConstraintViolation<Demo2>> violationSet = validator.validate(demo2);
    for (ConstraintViolation<Demo2> model : violationSet) {
        System.out.println(model.getMessage());
    }
}
```  
* 对象级联校验  
```java
@Data
public class Demo2 {
    @Size(min = 3,max = 5,message = "list的Size在[3,5]")
    private List<String> list;

    @NotNull
    //对象内部包含另一个对象作为属性，属性上加@Valid，可以验证作为属性的对象内部的验证
    @Valid
    private Demo3 demo3;
}

@Data
public class Demo3 {
    @Length(min = 5, max = 17, message = "length长度在[5,17]之间")
    private String extField;
}

//使用
/**前面配置了快速失败返回的Bean*/
@Autowired
private Validator validator;

@RequestMapping("/demo3")
public void demo3(){
    Demo2 demo2 = new Demo2();
    demo2.setList(new ArrayList<String>(){{add("111");add("222");add("333");}});

    Demo3 demo3 = new Demo3();
    demo3.setExtField("22");
    demo2.setDemo3(demo3);
    Set<ConstraintViolation<Demo2>> violationSet = validator.validate(demo2);
    for (ConstraintViolation<Demo2> model : violationSet) {
        System.out.println(model.getMessage());
    }
}
```  
* 分组校验  
```java
//1.定义分组接口
public interface GroupA {
}
public interface GroupB {
}  

//2.定义model，并分组
@Data
public class Person {
    @NotBlank
    @Range(min = 1,max = Integer.MAX_VALUE,message = "必须大于0",groups = {GroupA.class})
    /**用户id*/
    private Integer userId;
    @NotBlank
    @Length(min = 4,max = 20,message = "必须在[4,20]",groups = {GroupB.class})
    /**用户名*/
    //Validator默认分组
    private String userName;
    @NotBlank
    @Range(min = 0,max = 100,message = "年龄必须在[0,100]",groups={Default.class})
    /**年龄*/
    private Integer age;
    @Range(min = 0,max = 2,message = "性别必须在[0,2]",groups = {GroupB.class})
    /**性别 0：未知；1：男；2：女*/
    private Integer sex;
}

//3.使用
@RequestMapping("/demo6")
public void demo6(@Validated({GroupA.class, GroupB.class}) Person p, BindingResult result){
    if(result.hasErrors()){
        List<ObjectError> allErrors = result.getAllErrors();
        for (ObjectError error : allErrors) {
            System.out.println(error);
        }
    }
}

//或者
@RequestMapping("/demo5")
public void demo5(){
    Person p = new Person();
    /**GroupA验证不通过*/
    p.setUserId(-12);
    /**GroupA验证通过*/
    //p.setUserId(12);
    p.setUserName("a");
    p.setAge(110);
    p.setSex(5);
    Set<ConstraintViolation<Person>> validate = validator.validate(p, GroupA.class, GroupB.class);
    for (ConstraintViolation<Person> item : validate) {
        System.out.println(item);
    }
}
```  
除了可以进行分组，还可以指定分组校验的前后顺序
```java
//定义分组顺序接口
@GroupSequence({GroupA.class, GroupB.class, Default.class})
public interface GroupOrder {
}

//使用
@RequestMapping("/demo8")
public void demo8(@Validated({GroupOrder.class}) Person p, BindingResult result){
    if(result.hasErrors()){
        List<ObjectError> allErrors = result.getAllErrors();
        for (ObjectError error : allErrors) {
            System.out.println(error);
        }
    }
}

//或者
@RequestMapping("/demo7")
public void demo7(){
    Person p = new Person();
    /**GroupA验证不通过*/
    //p.setUserId(-12);
    /**GroupA验证通过*/
    p.setUserId(12);
    p.setUserName("a");
    p.setAge(110);
    p.setSex(5);
    Set<ConstraintViolation<Person>> validate = validator.validate(p, GroupOrder.class);
    for (ConstraintViolation<Person> item : validate) {
        System.out.println(item);
    }
}
```  

上述的方法基本可以解决很多的验证问题了，但是对于一些比较特殊的需求，还可以使用自定义验证器  
```java

public enum CaseMode {
    UPPER,
    LOWER;
}

//首先需要自定义一个注解
@Target( { ElementType.METHOD, ElementType.FIELD, ElementType.ANNOTATION_TYPE })
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = CheckCaseValidator.class)
@Documented
public @interface CheckCase {
    String message() default "";

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};

    CaseMode value();
}

//然后自定义校验器
public class CheckCaseValidator implements ConstraintValidator<CheckCase, String> {
    private CaseMode caseMode;
    public void initialize(CheckCase checkCase) {
        this.caseMode = checkCase.value();
    }

    public boolean isValid(String s, ConstraintValidatorContext constraintValidatorContext) {
        if (s == null) {
            return true;
        }

        if (caseMode == CaseMode.UPPER) {
            return s.equals(s.toUpperCase());
        } else {
            return s.equals(s.toLowerCase());
        }
    }
}


//model
public class Demo{
    @CheckCase(value = CaseMode.LOWER,message = "userName必须是小写")
    private String userName;

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }
}


//使用
@RequestMapping("/demo4")
public void demo4(){
    Demo demo = new Demo();
    demo.setUserName("userName");
    Set<ConstraintViolation<Demo>> validate = validator.validate(demo);
    for (ConstraintViolation<Demo> dem : validate) {
        System.out.println(dem.getMessage());
    }
}
```  

常用注解及含义  
| 注解 | 含义 |
| :----: | :----: | 
|@Null   |被注释的元素必须为 null|
|@NotNull    |被注释的元素必须不为 null|   
|@AssertTrue     |被注释的元素必须为 true|   
|@AssertFalse    |被注释的元素必须为 false|   
|@Min(value)     |被注释的元素必须是一个数字，其值必须大于等于指定的最小值    | 
|@Max(value)     |被注释的元素必须是一个数字，其值必须小于等于指定的最大值    | 
|@DecimalMin(value) |被注释的元素必须是一个数字，其值必须大于等于指定的最小值    | 
|@DecimalMax(value)  |被注释的元素必须是一个数字，其值必须小于等于指定的最大值   |  
|@Size(max=, min=)   |被注释的元素的大小必须在指定的范围内 |    
|@Digits (integer, fraction)     |被注释的元素必须是一个数字，其值必须在可接受的范围内     |
|@Past   |被注释的元素必须是一个过去的日期     |
|@Future    | 被注释的元素必须是一个将来的日期     |
|@Pattern(regex=,flag=)  |被注释的元素必须符合指定的正则表达式     |
Hibernate Validator 附加的 constraint     
| 注解 | 含义 |
| :----: | :----: | 
|@NotBlank(message =)   |验证字符串非null，且长度必须大于0     |
|@Email | 被注释的元素必须是电子邮箱地址     |
|@Length(min=,max=)  |被注释的字符串的大小必须在指定的范围内     |
|@NotEmpty  | 被注释的字符串的必须非空     |
|@Range(min=,max=,message=)  |被注释的元素必须在合适的范围内|