# MyBatis

[TOC]



## 自动generate

自动generate **model**，**Dao**以及**XML**

http://73082a16.wiz03.com/share/s/1P22Em2EB4g82RDqXL00R9u61GJirm0Y_QGd2HVtnX3jiWf1



## 动态SQL

动态 SQL 是 mybatis 的强大特性之一，也是它优于其他 ORM 框架的一个重要原因。mybatis 在对 sql 语句进行预编译之前，会对 sql 进行动态解析，解析为一个 BoundSql 对象，也是在此处对动态 SQL 进行处理的。在动态 SQL 解析阶段， #{ } 和 ${ } 会有不同的表现

在下面的语句中，如果 username 的值为 zhangsan，则两种方式无任何区别：

```
select * from user where name = #{name};1
select * from user where name = ${name};1
```

其解析之后的结果均为

```
 select * from user where name = 'zhangsan';1
```

但是 #{} 和 ${} 在预编译中的处理是不一样的。#{} 在预处理时，会把参数部分用一个占位符 ? 代替，变成如下的 sql 语句：

```
select * from user where name = ?;1
```

而 ${} 则只是简单的字符串替换，在动态解析阶段，该 sql 语句会被解析成

```
select * from user where name = 'zhangsan';1
```

以上，#{} 的参数替换是发生在 DBMS 中，而 ${} 则发生在动态解析过程中。

> 那么，在使用过程中我们应该使用哪种方式呢？
>
> 答案是，优先使用 #{}。因为 ${} 会导致 sql 注入的问题。看下面的例子：

```
select * from ${tableName} where name = #{name}1
```

在这个例子中，如果表名为

==user; delete user; –==

则动态解析之后 sql 如下：

```
select * from user; delete user; -- where name = ?;1
```

　　–之后的语句被注释掉，而原本查询用户的语句变成了查询所有用户信息+删除用户表的语句，会对数据库造成重大损伤，极大可能导致服务器宕机。

　　但是表名用参数传递进来的时候，只能使用 ${} ，具体原因可以自己做个猜测，去验证。这也提醒我们在这种用法中要小心sql注入的问题。