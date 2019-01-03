mongoDB CRUD Operations

```javaScript
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


```