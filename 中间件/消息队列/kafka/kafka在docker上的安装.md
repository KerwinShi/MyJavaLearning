利用docker镜像仓库中的已有的镜像快速搭建kafka集群

首先从docker的官方镜像仓库拉取需要的镜像文件：  
>由前面的单机测试环境安装过程可知，kafka需要zookeeper和kafka本身两部分  
```
docker pull wurstmeister/zookeeper  
docker pull wurstmeister/kafka
```

接下来就是启动容器：  
启动容器有两种方法：  
### 1.逐个启动（简单，操作步骤多）
>zookeeper是为kafka服务的，需要先启动zookeeper容器;然后启动kafka容器
```
docker run -d --name zookeeper -p 2181:2181 -t wurstmeister/zookeeper
docker run  -d --name kafka -p 9092:9092 -e KAFKA_BROKER_ID=0 -e KAFKA_ZOOKEEPER_CONNECT=192.168.99.100:2181 -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://192.168.99.100:9092 -e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 -t wurstmeister/kafka
```
>注意修改其中的ip为自己的宿主机的ip  

### 2.docker—compose启动（操作方便）
需要结合docker compse进行理解，直接操作出现异常，不知错误原因(其中一个在看了compose文件的定义后发现，是因为没有选择镜像文件，而是选择本地构建的镜像文件；另一个问题就是，在执行docker-compose的up命令后，容器没有成功启动，而是立即退出了)  

最后就是验证kafka集群的使用了：  
两种方法：  
### 1.命令行模式  
命令行下开启客户端与消费端，测试创建topic，发送数据到该topic并进行消费  

### 2.kafka tool  
使用kafka tool工具连接搭建的kafka集群，操作创建topic等  

### 3.java项目验证




参考：  
1.https://www.jianshu.com/p/8ccd712e2599（docker—compose启动）  
2.https://blog.csdn.net/sinat_39393179/article/details/105091237（逐个启动）  
3.https://www.cnblogs.com/answerThe/p/11267129.html（单机集群）  
4.https://www.cnblogs.com/answerThe/p/11290229.html（多机集群）   
