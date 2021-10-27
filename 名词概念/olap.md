## OLAP (On-Line Analytical Processing)  
olap是数据仓库的主要应用，主要是为使用者提供简单直接的查询结果，并支持复杂的分析功能，侧重决策支持  
oltp(On-Line Transaction Processing)主要用于进行事务性的处理，用频繁的增删查改来维护系统的基本功能（传统的关系性数据库）  
>举例说明：一个图书馆借阅系统，那么oltp就是这个系统背后的关系性数据库，记录了每个读者的姓名、读者证编号、借阅的图书等信息，每当读者借书、还书，就会发送增删改查的变化；olap就是针对于图书馆的管理者来说，他想知道哪一类型的图书更受欢迎，哪本书借出完的状态，这个是需要前面的关系型数据库进行比较复杂的**查询和分析**   

以上就是olap和oltp两种数据库系统的不同侧重点。目前市面上主流的**开源olap引擎**有：
* Hive、Hawq、Impala(基于SQL on Hadoop)
* Presto、Spark SQL类似(基于内存解析SQL生成执行计划)
* Kylin(用空间换时间、预计算)
* Druid(支持数据的实时摄入)
* ClickHouse(OLAP领域的Hbase，单表查询优势巨大)
* Greenpulm(OLAP领域的Postgresql)
