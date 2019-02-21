# MongoDB

[TOC]



## 建立MongoDB服务器

配置脚本 conf文件夹内的config文件，指定数据库、日志文件位置，以及端口等配置

```properties
#数据库路径
dbpath=D:\MongoDB\data
#日志输出文件路径
logpath=D:\MongoDB\datalogs\mongodb.log 
#错误日志采用追加模式，配置这个选项后mongodb的日志会追加到现有的日志文件，而不是从新创建一个新文件
logappend=true 
#启用日志文件，默认启用
journal=true 
#这个选项可以过滤掉一些无用的日志信息，若需要调试使用请设置为false
quiet=true 
#端口号 默认为27017
port=27017
```

配置**环境变量**

**管理员身份**运行

> mongod.exe --auth --config "C:\Program Files\MongoDB\Server\3.2\conf\mongodb.config" --install

第一次登录的时候不带auth命令，此时在admin下创建root用户

> mongod.exe --config "C:\Program Files\MongoDB\Server\3.2\conf\mongodb.config" --reinstall

需要进行多次的切换，切换的时候需要关闭后台服务mongoDB ，之后登录的时候重新打开

这里只列举最基本, 不做详细说明, 因为我也是学习者, 具体链接已给出, 详情可直接点击[权限链接](https://link.jianshu.com/?t=http://docs.mongoing.com/manual-zh/reference/built-in-roles.html#read) 

| 分类                                       | role(角色)                                                   | 简要说明                                                     |
| ------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 数据库用户角色(DB User Roles)              | `read` `readWrite`                                           | 为某个数据库创建一个用户, 分配该数据库的读写权力             |
| 数据库管理员角色(DB Admin Roles)           | `dbAdmin` `dbOwner` `userAdmin`                              | 拥有创建数据库, 和创建用户的权力                             |
| 集群管理角色(Culster Administration Roles) | `clusterAdmin` `clusterManager` `clusterMonitor` `hostManager` | 管理员组, 针对整个系统进行管理                               |
| 备份还原角色(Backup and Restoration Roles) | `backup` `restore`                                           | 备份数据库, 还原数据库                                       |
| 所有数据库角色(All-Database Roles)         | `readAnyDatabase` `readWriteAnyDatabase` `userAdminAnyDatabase` `dbAdminAnyDatabase` | 拥有对admin操作的权限                                        |
| Superuser Roles(超级管理员)                | `root`                                                       | `dbOwner` `userAdmin` `userAdminAnyDatabase`这几个角色角色提供了任何数据任何用户的任何权限的能力，拥有这个角色的用户可以在任何数据库上定义它们自己的权限 |

创建管理用户需要在admin数据库`use admin`下创建，之后

> db.createUser({ user: "root", pwd: "xxxxxxx", roles: [{ role: "root", db: "admin" }] })

总之最后服务中 可执行文件路径必须 带上auth参数 才能完成每次重启之后还需要密码登录

>  "C:\Program Files\MongoDB\Server\3.2\bin\mongod.exe" --auth --config "C:\Program Files\MongoDB\Server\3.2\conf\mongodb.config" --service



查询



查询













## 查询 find



























# MongoDB 聚合 aggregate

MongoDB中聚合(aggregate)主要用于处理数据(诸如统计平均值,求和等)，并返回计算后的数据结果。有点类似sql语句中的 count(*)。

------

## aggregate() 方法

MongoDB中聚合的方法使用aggregate()。

### 语法

aggregate() 方法的基本语法格式如下所示：

```mariadb
>db.COLLECTION_NAME.aggregate(AGGREGATE_OPERATION)
```

### 实例

集合中的数据如下：

```mariadb
{
   _id: ObjectId(7df78ad8902c)
   title: 'MongoDB Overview', 
   description: 'MongoDB is no sql database',
   by_user: 'runoob.com',
   url: 'http://www.runoob.com',
   tags: ['mongodb', 'database', 'NoSQL'],
   likes: 100
},
{
   _id: ObjectId(7df78ad8902d)
   title: 'NoSQL Overview', 
   description: 'No sql database is very fast',
   by_user: 'runoob.com',
   url: 'http://www.runoob.com',
   tags: ['mongodb', 'database', 'NoSQL'],
   likes: 10
},
{
   _id: ObjectId(7df78ad8902e)
   title: 'Neo4j Overview', 
   description: 'Neo4j is no sql database',
   by_user: 'Neo4j',
   url: 'http://www.neo4j.com',
   tags: ['neo4j', 'database', 'NoSQL'],
   likes: 750
},
```

现在我们通过以上集合计算每个作者所写的文章数，使用aggregate()计算结果如下：

```mariadb
> db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$sum : 1}}}])
{
   "result" : [
      {
         "_id" : "runoob.com",
         "num_tutorial" : 2
      },
      {
         "_id" : "Neo4j",
         "num_tutorial" : 1
      }
   ],
   "ok" : 1
}
>
```

以上实例类似sql语句：

```sql
select by_user, count(*) from mycol group by by_user
```

在上面的例子中，我们通过字段 by_user 字段对数据进行分组，并计算 by_user 字段相同值的总和。

下表展示了一些聚合的表达式:

| 表达式    | 描述                                           | 实例                                                         |
| --------- | ---------------------------------------------- | ------------------------------------------------------------ |
| $sum      | 计算总和。                                     | db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$sum : "$likes"}}}]) |
| $avg      | 计算平均值                                     | db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$avg : "$likes"}}}]) |
| $min      | 获取集合中所有文档对应值得最小值。             | db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$min : "$likes"}}}]) |
| $max      | 获取集合中所有文档对应值得最大值。             | db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$max : "$likes"}}}]) |
| $push     | 在结果文档中插入值到一个数组中。               | db.mycol.aggregate([{$group : {_id : "$by_user", url : {$push: "$url"}}}]) |
| $addToSet | 在结果文档中插入值到一个数组中，但不创建副本。 | db.mycol.aggregate([{$group : {_id : "$by_user", url : {$addToSet : "$url"}}}]) |
| $first    | 根据资源文档的排序获取第一个文档数据。         | db.mycol.aggregate([{$group : {_id : "$by_user", first_url : {$first : "$url"}}}]) |
| $last     | 根据资源文档的排序获取最后一个文档数据         | db.mycol.aggregate([{$group : {_id : "$by_user", last_url : {$last : "$url"}}}]) |

------

## 管道的概念

管道在Unix和Linux中一般用于将当前命令的输出结果作为下一个命令的参数。

MongoDB的聚合管道将MongoDB文档在一个管道处理完毕后将结果传递给下一个管道处理。管道操作是可以重复的。

表达式：处理输入文档并输出。表达式是无状态的，只能用于计算当前聚合管道的文档，不能处理其它的文档。

这里我们介绍一下聚合框架中常用的几个操作：

- `$project`：修改输入文档的结构。可以用来重命名、增加或删除域，也可以用于创建计算结果以及嵌套文档。
- `$match`：用于过滤数据，只输出符合条件的文档。`$match`使用MongoDB的标准查询操作。
- `$limit`：用来限制MongoDB聚合管道返回的文档数。
- `$skip`：在聚合管道中跳过指定数量的文档，并返回余下的文档。
- `$unwind`：将文档中的某一个数组类型字段拆分成多条，每条包含数组中的一个值。
- `$group`：将集合中的文档分组，可用于统计结果。
- `$sort`：将输入文档排序后输出。
- `$geoNear`：输出接近某一地理位置的有序文档。

### 管道操作符实例

1、`$project`实例

```mariadb
db.article.aggregate(
    { $project : {
        title : 1 ,
        author : 1 ,
    }}
 );
```

这样的话结果中就只还有_id,tilte和author三个字段了，默认情况下_id字段是被包含的，如果要想不包含_id话可以这样:

```
db.article.aggregate(
    { $project : {
        _id : 0 ,
        title : 1 ,
        author : 1
    }});
```

2.`$match`实例

```
db.articles.aggregate( [
                        { $match : { score : { $gt : 70, $lte : 90 } } },
                        { $group: { _id: null, count: { $sum: 1 } } }
                       ] );
```

`$match`用于获取分数大于70小于或等于90记录，然后将符合条件的记录送到下一阶段`$group`管道操作符进行处理。

3.`$skip`实例

```
db.article.aggregate(
    { $skip : 5 });
```

经过`$skip`管道操作符处理后，前五个文档被"过滤"掉。

```js
db.getCollection('device_history_state').aggregate([
{
    $match : { 
        LSZT_START_TIME : {
            $gte : 1534953600000, $lt : 1535040000000 } 
            },
//         LSZT_SB_CODE:"00000002_00000399"
},
{
    $group : {
        _id : {"LSZT_SB_CODE":"$LSZT_SB_CODE","LSZT_STATE":"$LSZT_STATE"},
        LSZT_SB_CODE : {$last : "$LSZT_SB_CODE"},
        LSZT_STATE : {$last : "$LSZT_STATE"},
        LSZT_END:{$sum:"$LSZT_END_TIME"},
        LSZT_START:{$sum:"$LSZT_START_TIME"},
//         val:{ $subtract : [ "$LSZT_END_TIME", "$LSZT_START_TIME" ] }
        }
}])
```





