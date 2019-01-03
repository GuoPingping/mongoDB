mongoDB常用命令

```
【参考：https://docs.mongodb.com】
```
```
    MongoDB:
        非关系型数据库，文档型数据库
        存储方式：虚拟内存+持久化
        适合场景：事件的记录、内容管理或博客平台等；
        架构特点：可以通过副本集，以及分片来实现高可用；
        数据处理：数据是存储在硬盘上的，只不过需要经常读取的数据会被加载到内存中，将数据存储在物理内存中，达到高度读写；

        基于分布式文件存储的开源数据库，由C++语言编写。旨在为web应用提供提供可扩展的
        高性能数据存储解决方案。
        在高负载情况下，添加更多的节点，可以保证服务器性能。

        特点：
            面向文档存储的数据库，操作起来比较简单容易；
            可以设置任何属性的索引，来实现更快的排序；
            可以通过本地或网络创建数据镜像，使得mongoDB有更强的扩展性；
            如果负载的增加，需要更多的存储空间和更强的处理能力，可以分布在计算机网络中其他节点上这就是所谓的分片；
            Map、reduce对数据进行批量处理和聚合操作；
            Map函数调用emit(key,value)遍历集合中所有记录，将key与value传给reduce函数进行处理；
            JSON形式的标记，可轻易查询文档中内嵌的数组及对象；
        优势：
            快速。在适量级的内存的mongodb的性能是非常迅速的，它将热数据存储在物理内存中，使得热数据的读写变得十分快；
            高扩展。高可用与集群架构拥有十分高的扩展性，通过物理机器的增加，及sharding的增加，mongodb的扩展将达到十分惊人的地步；
            自身的failover机制。mongodb的副本集配置中，当主库遇到问题，无法继续提供服务的时候，副本集将选举出一个新的主库来继续提供服务；
            json的存储格式。写入方便，如日志，适合文档格式的存储与查询。
        缺点：
            不支持事务，开发文档不完全不完善


    db.table_name.insertMany([
        { item: "journal", qty: 25, status: "A",
        size: { h: 14, w: 21, uom: "cm" }, tags: [ "blank", "red" ] },
    { item: "notebook", qty: 50, status: "A",
        size: { h: 8.5, w: 11, uom: "in" }, tags: [ "red", "blank" ] },
    { item: "paper", qty: 100, status: "D",
        size: { h: 8.5, w: 11, uom: "in" }, tags: [ "red", "blank", "plain" ] },
    { item: "planner", qty: 75, status: "D",
        size: { h: 22.85, w: 30, uom: "cm" }, tags: [ "blank", "red" ] },
    { item: "postcard", qty: 45, status: "A",
        size: { h: 10, w: 15.25, uom: "cm" }, tags: [ "blue" ] }
    ])

    db.table_name.insertOne()

    db.table_name.find({})

查询嵌套:
    db.table_name.find({'size.uom':'in'}})

数组查询
    db.table_name.find({'tags':'red'}})
    db.table_name.find({'tags':['red','blank']}})

Databases
    use myDB

capped collections固定集合
    事先创建大小固定的集合；
    像环形队列，空间不足，最早的文档就会被删除，为新的文档腾出空间；
    size 集合大小，单位kb，自动按照插入的顺序返回文档，max 限制文档个数；
    当指定文档数量上限时，同时指定大小；

    db.createCollection("log",{capped:true,size:10000})
    db.createCollection("log",{capped:true,size:10000,max:5000})

    db.cappedCollection.find().sort()

Check if a collection is capped
    db.collection.isCapped()

Convert a Collection to Capped
    db.runCommand({"convertToCapped":"mycoll",size:100000})



```