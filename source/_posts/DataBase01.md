---
title: MongoDB 基础命令语法
category:
  - MongoDB
tags:
  - MongoDB
  - MySQL
date: 2017-08-14 21:04:20
---
MongoDB 是一个基于分布式文件存储的数据库。由C++语言编写。旨在为WEB应用提供可扩展的高性能数据存储解决方案。MongoDB是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。
<!-- more -->
![MongoDB](https://gss2.bdstatic.com/-fo3dSag_xI4khGkpoWK1HF6hhy/baike/c0%3Dbaike80%2C5%2C5%2C80%2C26/sign=a5c7b08df603918fc3dc359830544df2/3b292df5e0fe99257ad18e3d35a85edf8cb171fa.jpg)
### 前言

今天我们来简单梳理一下其基础的命令语法。顺带与SQL做个简单的对比。

### 1. 基础概念

我们先来了解一下基础概念：

|  SQL 术语/概念  | MongoDB 术语/概念 |  解释/说明  |
|  : --- : | : ---- : | : ----- :|
| database | database | 数据库 |
| table | collection | 数据库 表/集合 |
| row | document | 数据表 行/文档 |
| column | field | 数据 字段/域 |
| index | index | 索引 |
| primary key | primary key | 主键，MongoDB自动将 _id 置为主键 |

### 2. 查询

#### 2.1 查询所有结果
> select * from table_name
>
> db.collection_name.find()

    下面的 table_name 和 collection_name 都以 article 代替

#### 2.2 指定返回
> select title , auther from article
>
> db.article.find({ },{'title': 1,'auther': 1})
> //find的第一个参数为查询条件，第二个参数是用来控制输出的，1表示返回，而0则
> 不返回。默认值是0，但_id是例外，当  `'_id': 0 `时，默认才不会显示_id。

#### 2.3 where
> select * from article where title = 'mongodb'
>
> db.article.find({'title' : 'mongodb'})

#### 2.4 and (与)
> select * from article where title = "mongodb" and author = "god"
>
> db.article.find({'title':'mongodb','author':'god'})

#### 2.5 or (或)
> select * from article where title = "mongodb" or author = "god"
>
> db.article.find({'$or': [{'title':'mongodb'},{'author':'god'}]})

#### 2.6 比较
> select * form article where read <= 100
>
> db.article.find({'read':{'$lte':100}})
> //其中 $gt (>),$lt (<),$gte (>=),$lte (<=)


#### 2.7 in
> select * from article where author in ('a','b','c')
>
> db.article.find({'auther': {'$in':['a','b','c']}})

#### 2.8 like
> select * from article where title like '%mongodb%'
>
> db.article.find({'title':/mongodb/})

#### 2.9 count
> select count(*) from article
>
> db.article.count()

#### 2.10 不等于
> select * from article where author != 'a'
>
> db.article.find({'author':{'$ne': 'a'}})

#### 2.11 排序
> // 降序/升序
> select * from article where type = 'mongodb' order by read desc/asc
>
> db.article.find({'type':'mongodb'}).sort({'read': -1/1}) //默认为1

    findOne() 查询返回一条数据，其余使用方式和 find() 一直

### 3. insert（插入）
> insert into artical (author,content,types) values (park','helloworld','mongodb')
>db.article.insert({'author':'park','content','helloworld','types':'mongodb'})

### 4. update（更新）
> //语法
> db.collection_name.update(query,update[,options])

* query: 必选，查询条件，类似find中的查询条件。
* update : 必选，update的对象和一些更新的操作符（如$,$inc...）等
* options：可选，一些更新配置的对象。
* upsert：可选，这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。
* multi：可选，mongodb 默认是false,只更新找到的第一条记录，如果这个参数为true, 就把按条件查出来多条记录全部更新。
* writeConcern：可选，抛出异常的级别。

> //简单更新
> update article set title = "mongodb" where read > 100
>
> db.article.update({"read": {"$gt": 100}}, {"$set": { "title": "mongodb"}})

更新特定字段 `$set`
> update game set count = 10000 where id = 123
>
> db.game.update({"_id": 123}, { "$set": {"count": 10000}})

删除特定字段 `$unset`
> db.game.update({"_id":123}, {"$unset": {"author":1}})
    $unset 指定字段的值是合法值即可


> db.article.update({title:'Seven'}, {$set:{read:134371}})
> //递增 `$inc`
> db.article.update({title:'Seven'}, {$inc:{read:2}}) // 每次加2


多个同时更新，要设置 `{multi:true}` 如：
> db.article.update({ }, {$inc:{read:10}},{multi:true})

数组追加，则应该用 `$push` 如：
> db.article.update({'title':'Seven'}, {$push:{'types':'mysql'}})
> db.article.update({'title':'Seven'}, {$push:{'types':['mysql','nosql']}})
> //注：追加字段必须是数组。如果数组字段不存在，则自动新增，然后追加。

### 5. 删除
> //删除所有文档
> delete from article
>
> db.article.remove()
    db.article.drop() //当删除一个集合中的所有文档时，直接删除一个集合效率更高

> //删除指定文档
> delete from artical where title = 'seven'
>
> db.article.remove({'title': 'seven'})
> db.article.remove({'title': 'seven'},1) //删除第一个

### 6. 建立索引
为文档中的一些key加上索引(index)可以加快搜索速度。
> db.article.ensureIndex({'title':1}) //升序1，降序-1

> db.article.getIndexs() //将返回所有索引，包括其名字。

> db.article.dropIndex('index_name') //删除索引

### 7. 文本搜索
对文本搜索之前，我们需要先对要搜索的key建立一个text索引。假定我们要对标题进行文本搜索，我们可以先这样：
> db.article.ensureIndex({title:'text'})

比如，查找带有"seven"的标题：
> db.article.find({text:{'$search':'seven'}})
> //MongoDB目前支持15种语言的文本搜索，暂不支持中文。

### 8. 常用命令
第一组：
> db.article.find().limit(20) // `limit()` 返回限制的数量
>
> // `skip()` 跳过数据条数，此处从第六条开始返回
> db.article.find().skip(5)

第二组：
> //可以结合limit()和skip()来达到分页效果：
> select * from article limit 10, 20
>
> db.article.find().skip(10).limit(20)

第三组：
> db.article.find().skip(5).limit(10).sort({'title':1})

第四组：
> //`pretty()` 输出的是经格式美化后的数据
> db.article.find().pretty()

第五组：
> show databases //显示数据库
> use database_name //切换/新建数据库
> show collections // 显示当前数据库下的集合
> db // 显示当前数据库

参考资料:[参考资料1] (http://ghmagical.com/article/page/id/Bj7qgmJ3CJUE)
[参考资料2](https://github.com/StevenSLXie/Tutorials-for-Web-Developers/blob/master/MongoDB%20%E6%9E%81%E7%AE%80%E5%AE%9E%E8%B7%B5%E5%85%A5%E9%97%A8.md)
