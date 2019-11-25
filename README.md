# Docker 与 MongoDB

## 使用 docker 容器运行 MongoDB

- 下载 MongoDB 的官方镜像
  - `docker pull mongo:4`

* 启动一个 MongoDB 服务器容器(参考`https://www.lmaye.com/2019/05/06/20190506232452/`和`https://docs.docker.com/docker-for-mac/osxfs/`)

  ```
  sudo docker run -p 27017:27017 --name mymongo -v ~/Desktop/workspace/mymongo/data/:/data/db -d mongo:4
  ```

* 查看 docker 容器状态
  - `docker ps`
* 查看数据库服务器日志
  - `docker logs mymongo`
* 启动一个已经停止的容器
  - `docker start 容器ID或容器名`
* 优雅的停止正在运行的容器
  - `docker stop 容器ID或容器名`
* 直接关闭容器
  - `docker kill 容器ID或容器名`
* 重启一个容器
  - `docker restart 容器ID或容器名`

## Mongo Express(一个基于网络的 MongoDB 数据库管理界面)

- 下载 mongo-express 镜像
  - `docker pull mongo-express`
- 运行 mongo-express
  - `docker run —link mymongo:mongo -p 8081:8081 mongo-express`

## Mongo shell(用来操作 mongodb 的 javascript 客户端界面)

- 运行 mongo shell
  - `docker exec -it mymongo mongo`

## 创建文档

- db.collection.insertOne() 创建单一文档

  ```
    db.collection.insertOne(<document>, {
      writeConcern: <document>
    })

    * <document>: 要写入的文档本身
    * writeConcern: 定义了文档创建操作的安全写级别。简单来说，安全写级别用来判断一次数据库写入操作是否成功。安全写级别越高，丢失数据的风险就越低，然而写入操作的延迟也可能更高。如果不提供writeConcern文档，mongoDB使用默认的安全写级别。
  ```

- db.collection.insertMany() 创建多个文档

  ```
    db.collection.insertMany([<document1>, <document2>, ...],
      {
        writeConcern: <document>,
        ordered: <boolean>
      }
    )

    * ordered: 用来决定mongoDB是否要按顺序来写入这些文档
    如果将ordered参数设置为false，mongoDB可以打乱文档写入的顺序，以便优化写入操作的性能
    ordered参数的默认值为true
  ```

- db.collection.insert() 创建单一或多个文档

  ```
    db.collection.insert(<document or array of documents>,
      {
        writeConcern: <document>,
        ordered: <boolean>
      }
    )
  ```

- db.collection.save() 创建单一文档

  ```
    db.collection.save(<document>,  {
      writeConcern: <document>
    })

    * db.collection.save()命令处理一个新文档的时候，它会调用db.collection.insert()命令
  ```

- 对象主键 ObjectId
- 复合主键

## 读取文档

- db.collection.find()读取文档

  ```
    db.collection.find(<query>, <projection>)

    * <query>: 定义了读取操作时筛选文档的条件
    * <projection>: 定义了对读取结果进行的投射
  ```

- 比较操作符

  ```
    { <field>: { $<operator>: <value> } }

    * $eq 匹配字段值相等的文档
    * $ne 匹配字段值不等的字段
    * $gt 匹配字段值大于查询值的文档
    * $gte 匹配字段值大于或等于查询值的文档
    * $lt 匹配字段值小于查询值的文档
    * $lte 匹配字段值小于或等于查询值的文档
    * $in 匹配字段值与任一查询值相等的文档
    * $nin 匹配字段值与任一查询值都不相等的文档
  ```

- 逻辑操作符

  - \$not 匹配筛选条件不成立的文档
    `{ field: { $not: { <operator-expression>} } }`
  - \$and 匹配多个筛选条件全部成立的文档
    `{ field: { $and: [{<expression1>}, {<expression2>},... ] } }`
  - \$or 匹配至少一个筛选条件成立的文档
    `{ field: { $or: [{<expression1>}, {<expression2>},... ] } }`
  - \$nor 匹配多个筛选条件全部不成立的文档
    `{ field: { $nor: [{<expression1>}, {<expression2>},... ] } }`

* 字段操作符
* 数组操作符

  - \$all 匹配数组字段中包含所有查询值的文档
    `{ field: { $all: [<value1>, <value2>, ...] }}`

  - \$elemMatch 匹配数组字段中至少存在一个值满足筛选条件的文档
    `{ field: { $elemMatch: [<query1>, <query2>, ...] }}`

- 运算操作符

  - \$regex 匹配满足正则表达式的文档

    ```
    { field: { : /pattern/, : '<options>'}}
    { field: { : /pattern/<options> }}

    * 兼容PCRE v8.41正则表达式库
    * 在和$in操作符一起使用时，只能使用/pattern/<options>
    ```

- 文档游标函数
  db.collection.find()返回一个文档集合游标，在不迭代游标的情况下，只列出前 20 个文档。可以使用游标下标直接访问文档集合中的某一个文档。游历完游标中的所有文档之后，或者在 10 分钟之后，游标便会自动关闭，可以使用 noCursorTimeout()函数来保持游标一直有效。

  - cursor.hasNext()
  - cursor.next()
  - cursor.forEach()
  - cursor.limit()
  - cursor.skip()
  - cursor.count()

    ```
      cursor.count(applySkipLimit)

      * 默认情况下，applySkipLimit为false，即cursor.count()不会考虑cursor.limit()和cursor.skip()的效果
    ```

  - cursor.sort()

    ```
      cursor.sort({ field: ordering })

      * ordering: 1表示由小及大的正向排序，-1表示逆向排序

    ```

  cursor.skip()在 cursor.limit()之前执行；cursor.sort()在 cursor.skip()和 cursor.limit()之前执行

- 文档投影操作

  ```
    db.collection.find(query, projection)

    * 不使用投影时，db.collection.find()返回符合筛选条件的完整文档
    * 使用投影可以有选择的返回文档中的部分字段
    * { field: inclusion } 1表示返回字段/0表示不返回字段

  ```

  除了文档主键之外，我们不可以在投影文档中混合使用包含和不包含这两种投影操作。要么列出所有应该包含的，要么列出所有不应该包含的字段

## 更新文档

- \$set(更新或新增字段)

  ```
    { $set: { <field1>: <value1>}}

    * 如果向现有数组字段范围以外的位置添加新值，数组字段的长度会扩大，未被赋值的数组成员将被设置为null
  ```

* \$unset(删除字段)

  ```
    { $unset: { <field1>: '', ... }}

    * $unset命令中的赋值（''）对操作结果没有任何影响
    * 如果$unset命令中的字段根本不存在，那么文档内容将保持不变
    * 当使用$unset命令删除数组字段中的某一个元素时，这个元素不会被删除，只会被赋以null值，而数组的长度不会改变
  ```

* \$rename(重命名字段)

  ```
    { $rename: { <field1>: <newName1>, ... }}

    * 如果$rename命令要重命名的字段并不存在，那么文档内容不会被改变
    * 如果新的字段名已经存在，那么原有的这个字段会被覆盖
    * 当$rename命令中的新字段存在的时候，$rename命令会先$unset新旧字段，然后再$set新字段
    * $rename命令中的旧字段和新字段都不可以指向数组元素
  ```

* \$inc
* \$mul

  - \$inc 和\$mul 只能应用于数字字段
  - 如果被更新的字段不存在，\$inc 会创建字段，并且将字段值设为命令中的增减值。而\$mul 会创建字段，但是把字段值设为 0

* \$min
* \$max
  - 比较之后更新值
  - 如果被更新的字段类型和更新值类型不一致，\$min 和\$max 按照 BSON 数据类型排序规则进行比较
    - Null
    - Numbers(ints, longs, doubles, decimals)
    - Symbol, String
    - Object
    - Array
    - BinData
    - ObjectId
    - Boolean
    - Date
    - TimeStamp
    - Regular Expression
