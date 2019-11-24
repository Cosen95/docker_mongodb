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

## Mongo Express(一个基于网络的 MongoDB 数据库管理界面)

- 下载 mongo-express 镜像
  - `docker pull mongo-express`
- 运行 mongo-express
  - `docker run —link mymongo:mongo -p 8081:8081 mongo-express`

## Mongo shell(用来操作 mongodb 的 javascript 客户端界面)

- 运行 mongo shell
  - `Docker exec -it mymongo mongo`

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
* 运算操作符
* 文档游标函数
* 文档投影操作

```

```
