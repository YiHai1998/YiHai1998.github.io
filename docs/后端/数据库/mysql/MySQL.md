## SQL语句

查询

```sql
select * from user where username=#{username} and password=#{password}
```

增

```sql
insert into user (username,password)
	values(#{username},#{password})
```







## 事务

### 什么是事务



### 事务的四大特性

> ACID



### 事务并发的问题



### 四大隔离特性



### MVCC



## 索引

### 数据结构





## MySQL锁



## MySQL优化