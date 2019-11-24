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
- db.collection.insertMany() 创建多个文档
- db.collection.insert() 创建单一或多个文档
- db.collection.save() 创建单一文档
- 对象主键 ObjectId
- 复合主键

## 读取文档

- db.collection.find()读取文档
- 比较操作符
- 逻辑操作符
- 字段操作符
- 数组操作符
- 运算操作符
- 文档游标函数
- 文档投影操作
