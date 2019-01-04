```javaScript

Aggregation
    聚合操作处理数据记录并返回计算结果。

    db.orders.aggregrate(
        [
            {$match:{status:"A"}},
            {$group:{_id:"$cust_id",total:{$sum:"$amount"}}}
        ]
    )

    和map-reduce的关系
        db.orders.mapReduce(
            function(){emit(this.cust_id,this.amount);},//map
            function(key,value){return Array.sum(values)},//reduce
            {
                query:{status:"A"},
                out:"order_totals" //输出
            }
        )
    管道
    $sort+$match
        如果sort后面跟着match，把match移到sort前面可以减少排序的对象个数
    $redact
        根据字段所处的document结构的级别，对文档进行修剪
        和”if-else“结合使用，即$cond 
            { $match: { year: 2014 } },
            { $redact: { $cond: { if: { $eq: [ "$level", 5 ] }, then: "$$PRUNE", else: "$$DESCEND" } } },
            { $match: { year: 2014, category: { $ne: "Z" } } }
    $project+$skip
        skip移到project前
            { $sort: { age : -1 } },
            { $skip: 5 },
            { $project: { status: 1, name: 1 } }
    $sort+$limit
        如果limit、sort相邻出现，mongo会使用TOP N计算方式来处理数据
            N较小，这些数据可以在内存中排序完成
            N较大，需要指定allowDiskUse为true
    $unwind
        将指定的数组结构拆解成多条document，Field必须是数组
    $out
        必须为pipeline最后一个stage，将结果写入到指定的collection
    $group
        $sum
        $avg
        $first
            返回每个group的第一条数据
        $last
        $max,$min
        $push
        $addToSet
            将表达式的值添加到一个集合中，无重复值
            
        

```