```javaScript
Indexes
    索引提高mongodb查询的效率，如果没有索引，mongodb在读取数据时，必须扫描集合中的每个文件
    并选取其中符合查询条件的记录。
    索引是特殊的数据结构，索引存储在一个易于遍历读取的数据集合中，是对数据库表中
    一列或多列的值进行排序的一种结构。

    Default id index
        独一无二的索引，不能删除

    创建一个索引：
        db.collection.createIndex({name:-1})

索引类型：
    Single Index 单键索引
        single fields , ascending/descending indexes 

    Compound Index 复合索引
        multiple fields 多字段搜索
        { userid: 1, score: -1 } //顺序很重要 value首先按照userid排序，再按照score排序

    Multikey Index 多键索引
        Arrays

    Geospatial Index 地理位置索引
        geospatial coordinate data
        2dsphere索引 球面几何的2d球体索引
        2d索引  平面几何的2d索引

    Text Index 文本索引
        searching for string content 
    
    Hashed Index 哈希索引
        键的值被散列，只支持相等匹配过滤器查询，并且不能在基于范围的查询上工作。

索引属性
    Unique Indexes 独一无二的索引
        当要求单个字段索引保持唯一时，他将拒绝该密钥的集合中已存在的值。
        db.members.createIndex( { "user_id": 1 }, { unique: true } )
    
    Partical Indexes 部分索引
        通过指定的过滤器查询将索引转换为部分索引。
        db.restaurants.createIndex(
            { cuisine: 1, name: 1 },
            { partialFilterExpression: { rating: { $gt: 5 } } }
        )

    Sparse Indexes 稀疏索引
        只包含索引字段的文档条目。完全跳过没有索引字段的文档
        部分索引优于稀疏索引
        db.addresses.createIndex( { "xmpp_id": 1 }, { sparse: true } )

    TTL Indexes
        在指定的时间从集合中删除文档，可以创建TTL索引。
        应用于 定期更新数据，使旧数据过时，对日后数据（日志数据）无用
        db.eventlog.createIndex( { "lastModifiedDate": 1 }, { expireAfterSeconds: 3600 } )
        db.log_events.createIndex( { "createdAt": 1 }, { expireAfterSeconds: 3600 } )

Index Build
    db.products.createIndex( { item: 1, quantity: -1 } )//item_1_quantity_-1
    db.products.createIndex( { item: 1, quantity: -1 } , { name: "inventory" } )//inventory

Manage Indexes
    View Existing Indexes
        db.people.getIndexes()
    List all Indexes for a Database
        db.getCollectionNames().forEach(function(collection){
            indexes=db[collection].getIndexes()
        })
    Remove a Specific Index
        db.accounts.dropIndex({"tax-id":1})//{"nIndexesWas":3,"ok":1}
    Modify an Index
        db.collection.reIndex()

Measure Index Use
    使用explain()返回查询计划
        db.collection.explain()
    控制使用指定的索引hint()
        db.people.find(
            { name: "John Doe", zipcode: { $gt: "63000" } }
        ).hint( { zipcode: 1 } ).explain("executionStats")

    $natural prevent mongodb from using any index
    db.people.find(
        { name: "John Doe", zipcode: { $gt: "63000" } }
    ).hint( { $natural: 1 } )

Indexing Strategies 索引策略
    适合的索引要考虑以下：
        查询的种类、读和写的比例、及系统中的可用内存量

    Create Indexes to support your queries 创建索引以支持查询

    Use Indexes to sort query results 使用索引来排序查询结果

    Ensure Indexes fit in ram 确保索引与内存相适应
        当所以可以整个存储于内存时，系统可以避免从磁盘读取索引，处理过程会更快。
 
    Create queries that ensure selectivity 创建能确保选择力的查询

Indexing Reference 索引参考
    db.collection.createIndex()
    db.collection.dropIndex()
    db.collection.dropIndexes() //删除所有索引
    db.collection.getIndexes() //文档数组，描述集合中现存的索引
    db.collection.reIndex() // 重建集合中所有现存的索引
    db.collection.totalIndexSize() //报告集合上索引使用的总大小

    cursor.explain()
    cursor.hint()
    cursor.max() //为游标指定索引的上界
    cursor.min() 
    cursor.snapshot() //强制游标使用——id键上的索引

    
```