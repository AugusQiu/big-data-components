## druid(德鲁伊)
分布式、实时分析、高性能数据存储系统，主要解决传统数据库无法大数据量查询的问题 
### 特点   
* 快速查询：druid提供快速聚合、快速olap查询能力，多租户的设计，面向用户分析应用；数据内存化，数据聚合粒度可以是1分钟、1小时、1天...  
* 实时数据注入：druid支持流数据的注入，提供数据的事件驱动，保证在实时和离线环境下事件的实效性、统一性  
> ps: 大数据处理系统可以分为 **批式(batch)** 大数据（历史数据）和 **流式(streaming)** 大数据（实时数据）两类。批量数据处理时间跨度是数十分钟到数小时之间，实时数据流的数据处理则是数百毫秒到数秒  
实时计算：用户期待立刻得到结果，比如支付宝查看一年消费额度，服务器集群可以立刻计算出结果
离线计算：用户把任务丢给计算机之后，结果不用马上出来，自己该去干嘛就去干嘛  
流式计算：速度介于实时、离线直接，强调的是一种计算方法  
例：服务器端，数据库保存一个值记录订单数量  
实时计算：用户每买一件东西，服务端立即发出一个交易成功的事件，该值接收到这个事件后就立即加1，状态是实时更新的  
离线计算：只有当用户主动去查询的时候，才慢腾腾的从低速存储中，把该用户所有的订单取出来，统计数量  

* 可扩展的PB级存储：扩容PB级数据量很方便，每秒百万级别的数据注入。即使是在加大数据规模的情况下，也能保证其时效性，druid按照时间范围把聚合数据进行分区处理  
* 多环境部署：druid可以运行在硬件机器，也可以运行在云上，多种数据系统包括 hadoop、spark、kafka等都可以注入数据到druid上  
 
### 使用场景
* 查询多，修改少
* 查询要求快  
* 查询以聚合或分组为主（druid中一般就是聚合后的数据）
* 数据源需支持实时和离线  
总的来说：druid适合实时报表、实时大屏展示，Druid通常用于BI(商务智能分析)，与Hive之类的SQL_ON_Hadoop引擎不同，Druid专为高并发性和亚秒级查询而设计，可通过UI进行交互式数据探索

### 数据格式
druid是摄入数据之前，首先需要定义一个数据源（Datasource），Druid的数据存储在数据源中（类似关系型数据库的表），这个dataSource的结构是 时间列（TimeStamp)、维度列（Dimension)、指标列（Metric)  
* 时间列：druid会将时间相近的数据聚合到一起，查询的时候先指定时间范围  
* 维度列：作为标识，比如：访问某个网站，维度可以是 访问的来源地址、浏览器种类
* 指标列：就是用于聚合和计算的列，包括count、sum等等，比如：访问的次数、每次会话浏览页数  
> Druid数据没有主键和唯一建的概念，它尽可能的把数据拼成一张大宽表（成百上千的列）来进行存储

### 数据摄入
druid支持 实时 和 批处理 两种摄入方式
````
 实时摄入：    kafka  => 
                         Druid  => 统一查询
 批处理摄入：HDFS、CSV => 
````
### 数据查询
支持：原生（json)和sql查询

### 拓展一：Druid从kafka获取数据的大致流程
1. kafka创建topic
````js
kafka-topics.sh --create 
                --zookeeper 
                node-01:2181,
                node-02:2181,
                node-03:2181
                --replication--factor 1
                --partitions 1
                --topic fast_sales
````
2. 创建生产者
````js
kafka-producer.sh --broker-list 
                  node-01:9092,
                  node-02:9092,
                  node-03:9092
                  --topic fast_sales
````
3. 创建消费者
````js
kafka-consumer.sh --bootstrap-server
                  node-01:9092,
                  node-02:9092,
                  node-03:9092
                  --topic fast_sales 
                  --group topic_test1_g1
````
4. 向kafka生产数据
````js
{"timestamp":"2020-08-08T01:03.00z","category":"手机","areaName":"北京","monye":"1450"}
{"timestamp":"2020-08-08T01:03.00z","category":"手机","areaName":"北京","monye":"1450"}
{"timestamp":"2020-08-08T01:03.00z","category":"家电","areaName":"北京","monye":"1550"}
 
{"timestamp":"2020-08-08T01:03.00z","category":"手机","areaName":"深圳","monye":"1000"}
{"timestamp":"2020-08-08T01:03.01z","category":"手机","areaName":"深圳","monye":"2000"}
{"timestamp":"2020-08-08T01:04.01z","category":"手机","areaName":"深圳","monye":"2200"}
````
5. druid配置datasource数据源，连接kafka，消费数据
````
配置kafka地址： 
Bootstrap servers
   node-01:9092,node-02:9092,node-03:9092
配置Topic:
   fast_sales
````