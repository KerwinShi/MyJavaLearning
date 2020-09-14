在之前的版本中，用户需要在 FileAppender 中内置一个 PatternLayout。之后的版本中，FileAppender 以及子类需要一个 encoder 而不是 layout。


一、Encoder
=  
将日志转换为字节数组，同时写入到一个OutputStream中。  
版本0.9.19之前依赖layout将日志转换为string以后通过Writer输出。   
1.Encoder接口  
-  
```
public interface Encoder<E> extends ContextAware, LifeCycle {

  void init(OutputStream os) throws IOException;

  void doEncode(E event) throws IOException;

  void close() throws IOException;
}
```  
2.LayoutWrappingEncoder  
-  
encoder与layout 之间的桥梁。它实现了encoder接口并且包裹了一个layout，通过委托该layout将日志事件转换为字符串。

3.PatternLayoutEncoder  
-  
被限制用来包裹PatternLayout实例。


为了帮助解析日志文件，logback 可以将格式化字符串插入到日志文件的顶部。
```
<appender name="FILE" class="ch.qos.logback.core.FileAppender"> 
  <file>foo.log</file>
  <encoder>
    <pattern>%d %-5level [%thread] %logger{0}: %msg%n</pattern>
    <outputPatternAsHeader>true</outputPatternAsHeader>
  </encoder> 
</appender>
```  


二、Layout
=  
负责将日志事件转换为字符串。
```
public interface Layout<E> extends ContextAware, LifeCycle {
  String doLayout(E event);//接受一个表示日志事件的对象 (任何类型) 并返回一个字符串
  String getFileHeader();
  String getPresentationHeader();
  String getFileFooter();
  String getPresentationFooter();
  String getContentType();
}
```  

1.自定义layout并完成配置  
-  
继承LayoutBase\<ILoggingEvent>;  
覆写doLayout(ILoggingEvent event)方法；  
LayoutWrappingEncoder包裹自定义的layout类，传递给FileAppender。

2.PatternLayout  
-  
可以通过调整PatternLayout的转换模式来进行定制。   
转换模式由字面量与格式控制表达式(转换说明符)组成。
具体使用查阅手册即可，东西内容太杂太多，包含已经有的使用和可以自定义的。