###  Mysql中INSERT ... ON DUPLICATE KEY UPDATE

#### 一、前言
> 在日常业务开发中经常有这样一个场景，首先创建一条记录，然后插入到数据库；如果数据库已经存在同一主键的记录，则执行update操作，如果不存在，则执行insert操作；
> 这个操作可以在业务层做，也可以在数据库层面做；
> 业务层一般做法是先查询，如果不存在在插入，如果存在则更新，但是查询和插入不是原子性操作，在并发量比较高的时候，可能两个线程都查询某个记录不存在，所以会执行两次插入，然后其中一条必然会因为主键（这里说的主键不是递增主键）冲突而失败。
> 数据库层mysql中INSERT ... ON DUPLICATE KEY UPDATE就可以做这个事情，并且是原子性操作，本文就来讲解的使用。

#### 二、INSERT ... ON DUPLICATE KEY UPDATE命令
##### 2.1单条记录下使用
```sql
INSERT INTO t1 (a,b,c) VALUES (1,2,3)
  ON DUPLICATE KEY UPDATE c=c+1;
```
如上sql假如t1表的主键或者UNIQUE 索引是a，那么当执行上面sql时候，如果数据库里面已经存在a=1的记录则更新这条记录的c字段的值为原来值+1，然后返回值为2。如果不存在则插入a=1,b=2,c=3到数据库，然后返回值为1。

##### 2.2多记录下使用
```sql
INSERT INTO t1 (a,b,c) VALUES (1,2,3),(4,5,6)
  ON DUPLICATE KEY UPDATE c=VALUES(c);
```
#### 三、MyBatis下使用

- 在mapper.xml里面配置如下：
假设teamId和userId为联合唯一索引键

```xml
<insert id="batchInsert" parameterType="java.util.List">
    insert into user_team (id,team_id, user_id,create_by, create_time,company_id)
    values
    <foreach collection="list" item="item" index="index" separator=",">
      (#{item.id}, #{item.teamId}, #{item.userId}, #{item.createBy},now(),#{item.companyId})
    </foreach>
      ON DUPLICATE KEY UPDATE update_by = values(update_by),update_time = now()
</insert>
  
```

```java
    /**
     * 批量插入用户与用户组关系表
     * @param userTeams
     * @return
     */
    int batchInsert(List<UserTeam> userTeams);
```

```java
/**
 * @author 
 * 用户和组关系表
 */
@Data
public class UserTeam implements Serializable {

    private Long id;

    /**
     * 组id
     */
    private Long teamId;

    /**
     * 用户id
     */
    private Long userId;
    /**
     * 公司id
     */
    private long companyId;
}
```
Mybatis中 ON DUPLICATE KEY UPDATE用法
在MySQL数据库中，如果在insert语句后面带上ON DUPLICATE KEY UPDATE 子句，  
那插入的行与表与现有记录的惟一索引或主键中产生重复值，那么就会发生旧行的更新；如果插入的行数据与现有表中记录的唯一索引或者主键不重复，则执行新纪录插入操作。

|条件|操作|
|:---:|:---:|
|唯一索引/主键相同|更新原有数据|
|唯一索引/主键不同|插入新数据|

总结: 存在则更新,如果不存在则新增
```xml
<insert id="add">

    INSERT INTO t_purchase (order_id, mac_type, price, amount, type, file,inputer,description)
    VALUES
    <foreach collection="list" index="index" item="item" separator=",">
        (#{item.orderId}, #{item.macType}, #{item.price}, #{item.amount}, #{item.type}, #{item.file}, #{item.inputer}, IFNULL(#{item.description},DEFAULT(description)))
    </foreach>
    ON DUPLICATE KEY UPDATE
    order_id = VALUES(order_id),
    mac_type = VALUES(mac_type),
    price = VALUES(price),
    amount = VALUES(amount),
    type = VALUES(type),
    file = VALUES(file),
    inputer = VALUES(inputer),
    description = VALUES(description),
    enable = 1

</insert>

```