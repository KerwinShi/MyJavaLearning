***这是虚拟机上安装单机kafka，作为测试环境的安装记录：***

1.首先需要安装JDK环境  
=
1.1查看已安装的Java环境  
-  
```
yum list installed |grep java
```  

1.2删除已安装  
-  
```
yum -y remove #名称
```  

1.3查看Java版本  
-  
```
yum -y list java*
```  

1.4安装Java(安装到/usr/lib/jvm/)  
-  
```
yum install java-1.8.0-openjdk.x86_64 java-1.8.0-openjdk-devel.x86_64 
```

1.5查看安装  
- 
```
java -version
```

1.6配置环境变量  
-  
修改ect/profile，在最后添加内容  
```
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.222.b10-1.el7_7.x86_64
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin
```  
保存后退出，为了使文件修改生效，执行命令  
```
source /etc/profile
```  

1.7确认环境变量  
```
echo $JAVA_HOME
echo $CLASSPATH
echo $PATH
```


2.安装zookeeper  
=  
2.1下载安装包  
-  
```
wget 镜像地址
```  

2.2解压文件  
-  
```
tar -zxvf zookeeper-3.4.11.tar.gz
```

2.3修改配置文件zoo_sample.cfg  
-  
先复制一份文件，名为zoo.cfg  
修改data目录，并在对应位置创建目录()  
需要外网连接，修改`server.1 = ip/hostname:2888:3888 `  
```
dataDir=目录
```
2.4启动  
-  
```
zkServer.sh start
```

2.5查看状态  
-  
```
zkServer.sh status
```

3.安装kafka  
=  
3.1下载kafka  
-  
```
wget http://mirror.bit.edu.cn/apache/kafka/2.2.1/kafka_2.11-2.2.1.tgz
```

3.2解压  
-  
```
tar zxvf kafka_2.11-2.2.1.tgz
```

3.3修改配置文件
-  
注意：   
如果需要对外网开放，还需要修改advertised.listener、zookeeper.connection的ip与端口信息，并开放虚拟机9092端口（否则就会发生kafka连接超时）
```
telnet ip port  可以访问
```
```
broker.id=1
```


3.4启动kafka服务  
-  
```
bin/kafka-server-start.sh config/server.properties 
```

3.5测试使用  

创建topic
```
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
```  
查看topic
```
bin/kafka-topics.sh --list --zookeeper localhost:2181
```  

使用kafka-console-producer.sh 发送消息  
```
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test  
```

使用kafka-console-consumer.sh消费消息  
```
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
```



参考：  
JDK  
1.https://www.cnblogs.com/adawoo/p/11665532.html  
ZOOKEEPER  
2.https://www.cnblogs.com/zhiyouwu/p/11546097.html  
3.https://segmentfault.com/a/1190000022199813  
KAFKA  
4.https://blog.csdn.net/qq_28666081/article/details/92020989  

spring boot集成spring kafka测试   
5.https://www.jianshu.com/p/c9581f695d64  
中间出现问题，无法建立与kafka的连接  
* 确认虚拟机端口开放情况（9092）
* 确认zookeeper与kafka服务已启动  
* 确认zookeeper或kafka配置文件的相关网络配置项，进行排查   
问题解决：  
6.https://blog.csdn.net/weixin_45492007/article/details/102462526?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~all~first_rank_v2~rank_v25-3-102462526.nonecase&utm_term=kafka%20%E5%AF%B9%E5%A4%96%E6%9C%8D%E5%8A%A1%E7%AB%AF%E5%8F%A3  
7.https://www.cnblogs.com/heqiuyong/p/10460150.html