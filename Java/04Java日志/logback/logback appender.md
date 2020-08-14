appender作用是将日志事件输出。委托layout或者encoder对象对日志事件进行格式化。
appender必须实现接口ch.qos.logback.core.Appender
```
//继承FilterAttachable 接口，一个或多个过滤器可以附加到appender实例
//继承LifeCycle 接口，也就是说它继承了 start()、stop()、isStarted 方法
public interface Appender<E> extends LifeCycle, ContextAware, FilterAttachable {
    public String getName();
    public void setName(String name);
    //泛型参数E， logback-classic 模块里面，E 的类型是 ILoggingEvent；logback-access 模块里面，E 的类型是 AccessEvent
    void doAppend(E event);//将日志格式化后输出到指定目标位置
}
```  
![appender继承关系链](../../../image\logger\appender继承关系链.png)

1.AppenderBase
-  
一个抽象类，实现了Appender接口，提供基本方法供使用，是logback所有appender的父类。  
虽然AppenderBase是抽象类，但是实现了doAppend方法（线程安全）。  
针对这种同步不适合的情况，logback提供了ch.qos.logback.core.UnsynchronizedAppenderBase
```
public synchronized void doAppend(E eventObject) {
  // prevent re-entry.避免自身递归调用
  if (guard) {
    return;
  }
  try {
    guard = true;
    if (!this.started) {
      //会发出一个警告并返回
      if (statusRepeatCount++ < ALLOWED_REPEATS) {
        addStatus(new WarnStatus(
            "Attempted to append to non started appender [" + name + "].",this));
      }
      return;
    }
    //检查过滤器的结果，根据过滤器链的结果，事件可以被拒绝或者被接受
    if (getFilterChainDecision(eventObject) == FilterReply.DENY) {
      return;
    }
    // ok, we now invoke the derived class's implementation of append
    this.append(eventObject);
  } finally {
    //guard 被释放
    guard = false;
  }
}
```  
2.OutputStreamAppender  
-  
其他appender构建的基础服务，通常无法直接实例一个OutputStreamAppender。  
encoder属性决定使用哪种方式将事件写入OutputStreamAppender  
immediateFlush属性默认true，立即刷新输出流以确保日志被立即写入  

3.ConsoleAppender  
-  
将日志输出到控制台，就是通过system.out或者system.err输出，通过用户指定的encoder格式化日志事件  
encoder属性  
target属性，确定是用system.out（默认）还是system.err输出  
withJansi属性默认false，true激活彩色代码  

4.FileAppender  
-  
将日志输出到文件，通过file指定目标文件，文件存在根据append的值进行追加或者截断  
append属性默认true，追击日志事件到文件，否则截断  
encoder属性  
file属性为要写入文件的名称，如果文件不存在，则新建（为保证路径正常工作要注意转义）  
prudent属性默认false，严格模式开启后，日志事件会被安全写入文件，但是导致成本增加为原来的3倍左右  
immediateFlush属性  

这里出现一个时间戳的标签\<timestamp>  
key用来区分元素  
datePattern用于将时间格式化为字符串  
timeReference标识时间戳引用哪个时间，默认为解析配置文件时间，contextBirth上下文初始化时间。  

5.RollingFileAppender  
-  
轮转日志，在满足指定条件的时候，将日志输出到另外一个文件。  
两个重要的子组件分别为：  
RollingPolicy：负责轮转日志（接口）  
```
public interface RollingPolicy extends LifeCycle {
    public void rollover() throws RolloverFailure;//对日志文件进行归档
    public String getActiveFileName();//获取当前日志文件名称
    public CompressionMode getCompressionMode();//压缩模式
    public void setParent(FileAppender appender);//引用父类
}
```  
* TimeBasedRollingPolicy  
    基于时间的轮转策略，同时负责轮转行为与触发轮转，同时实现了 RollingPolicy 与 TriggeringPolicy 接口。
    fileNamePattern  
    maxHistory控制最多保留多少数量的归档文件，将会异步删除旧的文件。  
    totalSizeCap控制所有归档文件总的大小。当达到这个大小后，旧的归档文件将会被异步的删除。   
    cleanHistoryOnStart如果设置为 true，那么在 appender 启动的时候，归档文件将会被删除。默认的值为 false。  
* SizeAndTimeBasedRollingPolicy
    基于大小以及时间的轮转策略。  
* FixedWindowRollingPolicy  
    根据固定窗口算法重命名文件。  
    minIndex表示窗口索引的下界  
    maxIndex表示窗口索引的上界  
    fileNamePattern  
TriggeringPolicy：负责轮转日志的时机把握  
```
public interface TriggeringPolicy<E> extends LifeCycle {
  //方法接收当前活动的文件以及当前的日志事件作为参数。
  public boolean isTriggeringEvent(final File activeFile, final <E> event);
}
```  
* SizeBasedTriggeringPolicy
    观察当前活动文件的大小，如果已经大于了指定的值，它会给 RollingFileAppender 发一个信号触发对当前活动文件的轮转。    

file属性  
appenbd属性  
encoder属性  
prudent属性：FixedWindowRollingPolicy不支持该属性，RollingFileAppender在严格模式下要与TimeBasedRollingPolicy结合使用，不允许同时有一个JVM操作，其他JVM压缩，不允许修改file属性  
