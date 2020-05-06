### MyBatis查询，返回值Map或List<Map>

#### 一、返回值Map

1. mapper.xml

```xml
<select id="selectUserMapLimitOne" resultType="java.util.HashMap">
    select id,name
    from
    user order by id desc limit 1
</select>
```




2. 接口定义


```java
/**
 * 获取仅含一个的map（map的size=字段个数）
 * @return
 */  
Map<String,Object> selectUserMapLimitOne();
```




#### 二、返回值List<Map>

1. mapper.xml
```xml
<select id="selectUserMapList" resultType="java.util.HashMap">
    select id,name from
    user order by id desc limit 2
</select>
```

2. 接口定义
```java
/**
 * 获取map list
 * @return
 */  
List<Map<String,Object>> selectUserMapList();
```