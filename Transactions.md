```javaScript
 Transactions
    事务
    Mongodb4.0引入了多文档事务
        通过snapshot隔离，事务提供全局一致的数据结果，并且执行要么全部成功，要么全部失败来保证数据的完整性。

    在mongodb中，在一个单个文档上的操作是原子性的，然而，涉及多个文档的操作，通常被作为一个”事务“，而不是原子性的。
    进行一个多文档事务，必须和一个session对象绑定，通过 startTransaction/ CRUD / commitTransaction 三段式来进行。

    使用事务步骤：
        获取session：session = db.getMongo().startSession()
        开启事务：session.startTransaction()
        获得collection：yun1 = session.getDatabase(“database”).yun1
        操作：yun1.insert({“ggggggg”:1})
        回滚事务：session.abortTransaction()
        提交事务：commitTransaction

    mongodb有多种存储引擎，不同的存储引擎在实现ACID时候，使用不同的机制。
    mongodb从3.0开始默认使用WiredTiger引擎
    WiredTriger引擎可以针对单个文档来保证ACID特性，但是当需要操作多个文档的时候无法保证ACID，
    即无法提供事务支持。
    mongodb虽然不支持跨文档的事务，但是，依然可以在应用层来获取类似事务的支持

    单文档的ACID如何实现？？
        Intent Lock 意图锁
        Multiple granularity locking 多粒度锁机制

    跨文档的事务支持

    事务？？
        数据库事务通常指对数据库进行读或写的一个操作序列；

    目的：
        为数据库操作提供一个从失败到恢复正常状态的方法，同时提供了数据库即使在异常状态仍能保持一致性的方法；
        当多个应用程序在并发访问数据库时，可以在这些应用程序之间提供一个隔离方法；

    为什么使用事务
        可以保持数据的一致性和完整性（避免异常和错误等导致的数据信息异常）
        
    ACID
        原子性：所有的改变要么都做了，要么都没做
        一致性：数据保持一致性的状态
        隔离性：其他用户看不到部分改变
        持久性：一旦向用户确认了事务，数据就处于安全的状态


```