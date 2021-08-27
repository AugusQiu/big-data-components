## kafka
### kafka是什么？
一个处理流数据的平台，设计核心就是发布-订阅，生产消息、消费消息  
* 分布式：kafka作为一个集群运行在一个多个服务器（broker)上，这些服务器可以跨地域、跨机房  
* 备份：kafka的数据会复制到不同的服务上，当某台故障失效时，生产者、消费者转而使用其它的kafka  
### Topic
翻译一下，就是主题的意思，每条发布到kafka集群的消息都有其专属的主题，或者说类别（相当于发布-订阅模式中的订阅主题）  
物理机上不同Topic的消息是分开存储的；逻辑上，其实用户只关注指定消息的Topic能不能生产、消费，不关心真实数据存于何处  
### Partition
分区：物理上的概念，每个Topic包含一个或多个分区（具体数目一般根据 kafka节点cpu的总核数）  
注意这个物理，上面Topic有提到它物理上是可以分布式存储的，那么怎么实现，就是借助分区机制  
### Producer
负责发布消息到 Kafka broker
### Consumer
消息消费者，向Kafka broker读取消息的客户端
### Consumer Group
消费者可能有多个，有了这个消费者组的概念  
每个Consumer属于一个特定的Consumer Group（这个可以通过指定group name设置，不指定就属于默认的group)  
### Mirror
MirrorMaker是为kafka提供的跨数据中心的数据同步和**迁移方案**，做的工作也就是是先从Source Cluster源集群消费消息（读操作），再生产到Target Cluster目标集群（写操作）   
整个过程要做到数据不丢失、消息不堆积，这也是数据同步的根本要求 
### offset  
指某一个分区（partition）的偏移量  
offset区分生产offset、消费offset 
#### 生产offset 
<img src="https://ask.qcloudimg.com/http-save/yehe-6070864/1cttms6wic.png?imageView2/2/w/1620" width="300" height="300">

