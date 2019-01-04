mongoDB CRUD Operations

```javaScript

find Documents

    db.users.insertOne({
        name:'gpp',
        age:'25',
        address:'shanghai',
        sex:'girl'
    })

    db.users.find({
        age:{$gt:18},
        {name:1,address:1}
    }).limit(5)

    db.inventory.find( { status: { $in: [ "A", "D" ] } } )
    db.inventory.find( { status: "A", qty: { $lt: 30 } } )
    db.inventory.find( { $or: [ { status: "A" }, { qty: { $lt: 30 } } ] } )
    db.inventory.find( {
        status: "A",
        $or: [ { qty: { $lt: 30 } }, { item: /^p/ } ]   //==item like "p%"
    } )

    db.inventory.insertMany([
        { item: "journal", qty: 25, tags: ["blank", "red"], dim_cm: [ 14, 21 ] },
        { item: "notebook", qty: 50, tags: ["red", "blank"], dim_cm: [ 14, 21 ] },
        { item: "paper", qty: 100, tags: ["red", "blank", "plain"], dim_cm: [ 14, 21 ] },
        { item: "planner", qty: 75, tags: ["blank", "red"], dim_cm: [ 22.85, 30 ] },
        { item: "postcard", qty: 45, tags: ["blue"], dim_cm: [ 10, 15.25 ] }
    ]);
        db.inventory.find( { tags: ["red", "blank"] } )
        db.inventory.find( { tags: { $all: ["red", "blank"] } } )
        db.inventory.find( { tags: "red" } )
        db.inventory.find( { dim_cm: { $elemMatch: { $gt: 22, $lt: 30 } } } )   //两个条件都满足的记录才能查出来
        db.inventory.find( { "tags": { $size: 3 } } ) //根据数组的长度查询记录


    db.inventory.insertMany( [
        { item: "journal", instock: [ { warehouse: "A", qty: 5 }, { warehouse: "C", qty: 15 } ] },
        { item: "notebook", instock: [ { warehouse: "C", qty: 5 } ] },
        { item: "paper", instock: [ { warehouse: "A", qty: 60 }, { warehouse: "B", qty: 15 } ] },
        { item: "planner", instock: [ { warehouse: "A", qty: 40 }, { warehouse: "B", qty: 5 } ] },
        { item: "postcard", instock: [ { warehouse: "B", qty: 15 }, { warehouse: "C", qty: 35 } ] }
    ]);
        db.inventory.find( { "instock": { warehouse: "A", qty: 5 } } ) //等值匹配要给全所有的字段，且字段顺序也须相同
        db.inventory.find( { 'instock.qty': { $lte: 20 } } )
        db.inventory.find( { 'instock.0.qty': { $lte: 20 } } )//使用数组标号查询一个内嵌文档的字段
        //使用点标记为数组中指定位置的文档里的字段指定查询条件，下例选项所有instock数组中第1个文档的qty字段值小于20的文档：
        db.inventory.find( { "instock": { $elemMatch: { qty: 5, warehouse: "A" } } } )//使用$elemMatch操作符在文档数组上指定多个查询要求，选取出单个内嵌文档可以满足所有查询要求的文档
        db.inventory.find( { "instock": { $elemMatch: { qty: { $gt: 10, $lte: 20 } } } } )//数组中存在内嵌文档的qty大于10且小于20的文档
        db.inventory.find( { "instock.qty": { $gt: 10,  $lte: 20 } } )
        db.inventory.find( { "instock.qty": 5, "instock.warehouse": "A" } )

    db.inventory.insertMany( [
        { item: "journal", status: "A", size: { h: 14, w: 21, uom: "cm" }, instock: [ { warehouse: "A", qty: 5 } ] },
        { item: "notebook", status: "A",  size: { h: 8.5, w: 11, uom: "in" }, instock: [ { warehouse: "C", qty: 5 } ] },
        { item: "paper", status: "D", size: { h: 8.5, w: 11, uom: "in" }, instock: [ { warehouse: "A", qty: 60 } ] },
        { item: "planner", status: "D", size: { h: 22.85, w: 30, uom: "cm" }, instock: [ { warehouse: "A", qty: 40 } ] },
        { item: "postcard", status: "A", size: { h: 10, w: 15.25, uom: "cm" }, instock: [ { warehouse: "B", qty: 15 }, { warehouse: "C", qty: 35 } ] }
    ]);
        db.inventory.find( { status: "A" }, { item: 1, status: 1 } )//select _id,item,status from inventory where status="A"
        db.inventory.find( { status: "A" }, { item: 1, status: 1, _id: 0 } ))//select item,status from inventory where status="A"
        db.inventory.find( { status: "A" }, { status: 0, instock: 0 } )//返回除了status和instock以外的所有字段
        db.inventory.find(
            { status: "A" },
            { item: 1, status: 1, "size.uom": 1 }
        )//返回字段_id,item,status,size中的uom字段，并保持嵌入在size内
        db.inventory.find( { status: "A" }, { item: 1, status: 1, instock: { $slice: -1 } } )//返回istock字段中的最后一个元素

        db.inventory.insertMany([
            { _id: 1, item: null },
            { _id: 2 }
        ])
            db.inventory.find( { item: null } )//两个文档都满足
            db.inventory.find( { item : { $type: 10 } } )//匹配item字段存在，且值为null
            db.inventory.find( { item : { $exists: false } } )//item字段没有的文档


在mongo shell里迭代游标
    db.collection.find()返回的是一个游标，迭代该游标即可访问文档，如果没有使用var关键字为返回的游标分配变量的话，该游标会自动迭代20次输出结果；

    手动迭代游标
        在mongo shell，当使用var关键字将find返回的游标分配给变量时，游标不再自动迭代，使用next访问文档
        
        var myCursor=db.users.find({type:2})
        while(myCursor.hasNext()){
            print(tojson(myCursor.next()))
            ==printjson(myCursor.next())
        }
        myCursor.forEach() //迭代游标文档
    
    迭代器序列
        toArray()迭代游标，将文档返回到数组，将游标中所有文档载入内存，即排空该游标
    
    游标行为
        关闭非活动游标
            默认，mongo会自动关闭已经排空的游标，或非活动状态达到10分钟的游标，要在mongo shell里
            覆盖该行为，会使用cursor
            var myCursor = db.users.find().noCursorTimeout();
            noCursorTimeout不再因空闲超时自动关闭游标myCursor，必须使用cursor.close方法手动关闭，或排空该游标文档
                
```

```javaScript

Update Documents

    db.collection.updateOne(<filter>, <update>, <options>);
    db.collection.updateMany(<filter>, <update>, <options>);
    db.collection.replaceOne(<filter>, <update>, <options>);

    db.inventory.insertMany( [
        { item: "canvas", qty: 100, size: { h: 28, w: 35.5, uom: "cm" }, status: "A" },
        { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
        { item: "mat", qty: 85, size: { h: 27.9, w: 35.5, uom: "cm" }, status: "A" },
        { item: "mousepad", qty: 25, size: { h: 19, w: 22.85, uom: "cm" }, status: "P" },
        { item: "notebook", qty: 50, size: { h: 8.5, w: 11, uom: "in" }, status: "P" },
        { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" },
        { item: "planner", qty: 75, size: { h: 22.85, w: 30, uom: "cm" }, status: "D" },
        { item: "postcard", qty: 45, size: { h: 10, w: 15.25, uom: "cm" }, status: "A" },
        { item: "sketchbook", qty: 80, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
        { item: "sketch pad", qty: 95, size: { h: 22.85, w: 30.5, uom: "cm" }, status: "A" }
    ] );
        db.inventory.updateOne(
            {item:'paper'},
            {
                $set:{'size.uom':'cm',status:"p"},
                $currentDate:[lastModified:true]
            })//使用$currentDate操作符更新lastModified字段的值为当前日期，如果lastModified字段不存在，$currentDate将创建该字段
        db.inventory.updateMany(
            { "qty": { $lt: 50 } },
            {
                $set: { "size.uom": "in", status: "P" },
                $currentDate: { lastModified: true }
            }
        )
        db.inventory.replaceOne(
            { item: "paper" },
            { item: "paper", instock: [ { warehouse: "A", qty: 60 }, { warehouse: "B", qty: 40 } ] }
        )//替换除_id以外的整个文档

    更新行为
        Atomicity原子性
            所有的写操作在单个文档层面都是原子性的
        _id Field
            一旦赋值，将不能更改_id字段的值
        Document Size文档大小
            当更新或替换操作增大了文档大小并超过了该文档分配的空间时，mongodb会重新存储该文档
        Field Order字段排序
        Upsert Option更新插入操作
        Write Acknowledgement

```

```javaScript

Delete Documents

db.inventory.deleteMany({status: "D" })
db.inventory.deleteOne( { status: "D" } )

删除行为
    Indexes索引
        即使删除集合中所有文档，也不会移出索引
    Atomicity
    Write Acknowledgement

```

```javaScript

Text Search
    文本搜索

    Text Index
        db.stores.insert(
        [
            { _id: 1, name: "Java Hut", description: "Coffee and cakes" },
            { _id: 2, name: "Burger Buns", description: "Gourmet hamburgers" },
            { _id: 3, name: "Coffee Shop", description: "Just coffee" },
            { _id: 4, name: "Clothes Clothes Clothes", description: "Discount clothing" },
            { _id: 5, name: "Java Shopping", description: "Indonesian goods" }
        ]
        )
            db.stores.createIndex( { name: "text", description: "text" } )//创建Text Index，每个表只能拥有一个text index
            db.stores.find( { $text: { $search: "java coffee shop" } } )//查询包含java、coffee或shop的记录
            db.stores.find( { $text: { $search: "java \"coffee shop\"" } } )//查询包含java或coffee shop的记录
            db.stores.find( { $text: { $search: "java shop -coffee" } } ) //查询包含java或shop 但是不在coffee的记录
            db.stores.find(
                { $text: { $search: "java coffee shop" } },
                { score: { $meta: "textScore" } }
                ).sort( { score: { $meta: "textScore" } } )

```