# MongoDB 笔记


## 更新

### update

update 是更新操作，没有返回值

``` sql
db.collection.update(
   <query>,
   <update>,
   {
     upsert: <boolean>,
     multi: <boolean>,
     writeConcern: <document>
   }
)
```

参数说明：

* query : update 的查询条件，类似 sql update 查询内 where 后面的  
* update : update 的对象和一些更新的操作符（如\$,\$inc...）等，也可以理解为sql update 查询内 set 后面的  
* upsert : 可选，这个参数的意思是，如果不存在 update 的记录，是否插入 objNew , true 为插入，默认是 false，不插入  
* multi : 可选，mongodb 默认是false,只更新找到的第一条记录，如果这个参数为 true，就把按条件查出来多条记录全部更新  
* writeConcern :可选，抛出异常的级别

### findAndModify

findAndModify 有返回值，使用 new 选项可以返回修改前或修改后的文档

findAndModify 强调操作的原子性（atomically），比如用来实现自增1的操作或者操作队列。属于 get-and-set 式的操作，一般来讲，findAndModify 比 update 操作稍慢，因为需要等待数据库的响应。

mongodb 不支持事务，但提供了许多原子操作，文档的保存，修改，删除等都是原子操作

原子操作保证一个文档要么保存到 mongodb，要么没有保存到，不会出现查询到的文档没有保存完整的情况


``` sql
db.collection.findAndModify({
    query: <document>,
    sort: <document>,
    remove: <boolean>,
    update: <document>,
    new: <boolean>,
    fields: <document>,
    upsert: <boolean>,
    bypassDocumentValidation: <boolean>,
    writeConcern: <document>,
    collation: <document>,
    arrayFilters: [ <filterdocument1>, ... ]
});
```

参数说明：

* query:{}, 查询条件  
* update:{}, 更新文档，不能与 remove 同时出现  
* remove:true, 删除符合查询条件的文档，不能与 update 同时出现  
* new:true, true 表示返回更新后的文档，false 表示返回更新前的文档，默认 false  
* sort:{}, 排序条件  
* fields:{}, 投影操作，只返回想要的参数，与 find 的第二个参数一致  
* upsert:true, 如果不存在要 update 的记录，是否插入，true ：插入，false ：不插入

### $setOnInsert 操作符和 upsert:true

upsert:true:如果要更新的文档不存在的话会插入一条新的记录

\$setOnInsert 操作符会将指定的值赋值给指定的字段，

如果要更新的文档存在那么 \$setOnInsert 操作符不做任何处理；

``` dql
db.collection.update(

　　<query>,

　　{$setOnInsert:{<field1>:<value1>,......}},

　　{upsert:true}

)
```

\$set 和 \$setOnIsert 不能对同一字段使用  

如果该文档不存在，则会创建一个新的文档，\$set 和 \$setOnInsert 操作的键值都会插入进入  

如果文档存在，那么只会更新 \$set 操作符指定的值，忽略掉 \$setOnInsert 指定的值

实例:  
``` sql
db.products.update(

　　{_id:　　1},

　　{

　　　　$set:{item:"apple"},

　　　　$setOnInsert:{defaultQty:100}

　　},

　　{upsert:true}

)
```


### 原子操作常用命令

#### $set
更新键值，若键不存在则创建

> { $set : { field : value } }

#### $unset
删除一个键

> { $unset : { field : 1 } }

#### $inc
对值为数字的键进行加分运算，value 可以是正数也可以是负数

> { $inc : { field : value } }

#### $push
把 value 追加到 field 里面去，field 一定要是数组类型才行，如果 field 不存在，会新增一个数组类型加进去

> { $push : { field : value } }

#### $pushAll
同 **$push** ,只是一次可以追加多个值到一个数组字段内，value_array 是数组类型

> { $pushAll : { field : value_array } }

#### $pull
从数组 field 内删除一个等于 value 的元素

> { $pull : { field : value } }

#### $addToSet
增加一个值到数组内，而且只有当这个值不在数组内才增加。

> { $addToSet : { field : value } }

#### $pop
删除数组的第一个或最后一个元素

> { $pop : { field : 1 } } 删除最后一个元素  
> { $pop : { field : -1 } }  删除第一个元素

#### $rename
修改字段名称

> { $rename : { old_field_name : new_field_name } }

#### $bit
位操作，integer类型

> {$bit : { field : {and : 5}}}

#### 偏移操作符

> t.find() { "_id" : ObjectId("4b97e62bf1d8c7152c9ccb74"), "title" : "ABC", "comments" : [ { "by" : "joe", "votes" : 3 }, { "by" : "jane", "votes" : 7 } ] }
 
> t.update( {'comments.by':'joe'}, {$inc:{'comments.$.votes':1}}, false, true )
 
> t.find() { "_id" : ObjectId("4b97e62bf1d8c7152c9ccb74"), "title" : "ABC", "comments" : [ { "by" : "joe", "votes" : 4 }, { "by" : "jane", "votes" : 7 } ] }

## 参考链接

* [MongoDB 4.0手册](https://docs.mongodb.com/manual/)

