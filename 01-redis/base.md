
```shell
       #狂神redis视频地址: https://www.bilibili.com/video/BV1S54y1R7SB?p=2&spm_id_from=pageDriver
```

##  一、Nosql概述 

```shell
1. 什么是nosql 
        #nosql是 not only sql  不仅仅是sql的意思,泛指非关系型数据库。

2. nosql 特点
        #方便扩展,数据之间没有关系,很好扩展.
        #大数据量高性能(redis 一秒写8万次,读取11万次,nosql的缓存记录是一个细粒度的缓存,性能会比较高！)
        #数据类型是多样的,不需要事先设计数据库,随取随用,如果数据量十分大的表,很多人就无法设计了。


3.NOSQL和RDBMS区别
  MYSQL:
        -结构化组织.
        -SQL.
        -数据和关系都存在单独的表中.
        -操作数据定义语言.
        -严格的一致性.

 
  NOSQL:
        - 不仅仅是数据
        - 没有固定的查询语言
        - 键值对存储,列存储,文档存储,图形数据库(社交关系)
        - 是最终一致性
        -CAP定理和BASE(异地多活) 
        -高性能
        -高可扩展性

4. 什么是3V和3高

     大数据时代的3V: 主要是描述问题的.
      1. 海量Volume
      2. 多样variety
      3. 实时velociry

     大数据时代的3高: 主要对程序的要求.
      1. 高并发
      2. 高可拓
      3. 高性能 


```

##  二、架构师思维

```shell
    #如果未来相当架构师: 没有什么是加一层解决不了的.

    1. 商品的基本信息
       -名称,价格,商家信息:
       -关系型数据库就可以解决了.
       -mysql/oracle(淘宝早年就去ioe了,王坚:推荐文章 阿里云的这群疯子)
       -淘宝把mysql底层改造成了适用自己的mysql.

    2. 商品的描述、评论(文字比较多)
        -文档型数据库中mongodb

    3. 图片
        -分布式文档系统 FastDFS
        -淘宝自己的    TFS
        -google的     GFS
        -hadoop      HDFS
        -阿里云的      OSS

    4. 商品的关键字(搜索)
        -搜索引擎     solr elasticsearch
        -阿里的搜索   Iserach     

    5. 商品热门的波段信息
        -内存数据库
        -redis、Tair、memache

    6. 商品的交易,外部支付接口
        -第三方接口应用

```

##  三、nosql的四大类型

```shell
  1. KV键值对:
        -新浪: Redis
        -美团: Redis+Tair
        -阿里: Redis+memache
        -百度: Redis+memache
    
  2. 文档型数据库(bson和json一样):
        MongoDB
         -MongoDB 是一个基于分布式文件存储的数据库,c++编写,主要用来处理大量的文档！
         -MongoDB 是介于关系型和非关系中间产品,它是非关系型中功能最丰富,最像关系型数据库的.
        ConthDB
   3. 列存储
         -Hbase
         -分布式文件系统

   4. 图关系数据库
        -他不是存图形,放的是关系,比如: 朋友圈社交网络,广告推荐.
        -Neo4j、infoGrid


```