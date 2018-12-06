---
sidebarDepth: 3
---

# 条件构造器
::: tip 说明:
- 以下出现的第一个入参`boolean condition`表示该条件**是否**加入最后生成的sql中
- 以下代码块内的多个方法均为从上往下补全个别`boolean`类型的入参,默认为`true`
- 以下出现的泛型`This`均为具体使用的`Wrapper`的实例
- 以下方法在入参中出现的`R`为泛型,在普通wrapper中是`String`,在LambdaWrapper中是**函数**(例:`Entity::getId`,`Entity`为实体类,`getId`为字段`id`的**getMethod**)
- 以下方法入参中的`R column`均表示数据库字段,当`R`为`String`时则为数据库字段名(**字段名是数据库关键字的自己用转义符包裹!**)!而不是实体类数据字段名!!!
- 以下举例均为使用普通wrapper,入参为`Map`和`List`的均以`json`形式表现!
- 使用中如果入参的`Map`或者`List`为**空**,则不会加入最后生成的sql中!!!
- 有任何疑问就点开源码看,看不懂**函数**的[点击我学习新知识](https://www.jianshu.com/p/613a6118e2e0)
:::

## AbstractWrapper
::: tip 说明:
QueryWrapper(LambdaQueryWrapper) 和 UpdateWrapper(LambdaUpdateWrapper) 的父类  
用于生成 sql 的 where 条件, entity 属性也用于生成 sql 的 where 条件
:::

### allEq
``` java{3}
allEq(Map<R, V> params)
allEq(Map<R, V> params, boolean null2IsNull)
allEq(boolean condition, Map<R, V> params, boolean null2IsNull)
```
- 全部[eq](#eq)(或个别[isNull](#isnull))
::: tip 个别参数说明:
`params` : `key`为数据库字段名,`value`为字段值  
`null2IsNull` : 为`true`则在`map`的`value`为`null`时调用 [isNull](#isnull) 方法,为`false`时则忽略`value`为`null`的
:::
- 例1: `allEq({id:1,name:"老王",age:null})`--->`id = 1 and name = '老王' and age is null`
- 例2: `allEq({id:1,name:"老王",age:null}, false)`--->`id = 1 and name = '老王'`

``` java{3}
allEq(BiPredicate<R, V> filter, Map<R, V> params)
allEq(BiPredicate<R, V> filter, Map<R, V> params, boolean null2IsNull)
allEq(boolean condition, BiPredicate<R, V> filter, Map<R, V> params, boolean null2IsNull) 
```
::: tip 个别参数说明:
`filter` : 过滤函数,是否允许字段传入比对条件中  
`params` 与 `null2IsNull` : 同上
:::
- 例1: `allEq((k,v) -> k.indexOf("a") > 0, {id:1,name:"老王",age:null})`--->`name = '老王' and age is null`
- 例2: `allEq((k,v) -> k.indexOf("a") > 0, {id:1,name:"老王",age:null}, false)`--->`name = '老王'`

### eq
``` java{2}
eq(R column, Object val)
eq(boolean condition, R column, Object val)
```
- 等于 =
- 例: `eq("name", "老王")`--->`name = '老王'`

### ne
``` java{2}
ne(R column, Object val)
ne(boolean condition, R column, Object val)
```
- 不等于 <>
- 例: `ne("name", "老王")`--->`name <> '老王'`

### gt
``` java{2}
gt(R column, Object val)
gt(boolean condition, R column, Object val)
```
- 大于 >
- 例: `gt("age", 18)`--->`age > 18`

### ge
``` java{2}
ge(R column, Object val)
ge(boolean condition, R column, Object val)
```
- 大于等于 >=
- 例: `ge("age", 18)`--->`age >= 18`

### lt
``` java{2}
lt(R column, Object val)
lt(boolean condition, R column, Object val)
```
- 小于 <
- 例: `lt("age", 18)`--->`age < 18`

### le
``` java{2}
le(R column, Object val)
le(boolean condition, R column, Object val)
```
- 小于等于 <=
- 例: `le("age", 18)`--->`age <= 18`

### between
``` java{2}
between(R column, Object val1, Object val2)
between(boolean condition, R column, Object val1, Object val2)
```
- BETWEEN 值1 AND 值2
- 例: `between("age", 18, 30)`--->`age between 18 and 30`

### notBetween
``` java{2}
notBetween(R column, Object val1, Object val2)
notBetween(boolean condition, R column, Object val1, Object val2)
```
- NOT BETWEEN 值1 AND 值2
- 例: `notBetween("age", 18, 30)`--->`age not between 18 and 30`

### like
``` java{2}
like(R column, Object val)
like(boolean condition, R column, Object val)
```
- LIKE '%值%'
- 例: `like("name", "王")`--->`name like '%王%'`

### notLike
``` java{2}
notLike(R column, Object val)
notLike(boolean condition, R column, Object val)
```
- NOT LIKE '%值%'
- 例: `notLike("name", "王")`--->`name not like '%王%'`

### likeLeft
``` java{2}
likeLeft(R column, Object val)
likeLeft(boolean condition, R column, Object val)
```
- LIKE '%值'
- 例: `likeLeft("name", "王")`--->`name like '%王'`

### likeRight
``` java{2}
likeRight(R column, Object val)
likeRight(boolean condition, R column, Object val)
```
- LIKE '值%'
- 例: `likeRight("name", "王")`--->`name like '王%'`

### isNull
``` java{2}
isNull(R column)
isNull(boolean condition, R column)
```
- 字段 IS NULL
- 例: `isNull("name")`--->`name is null`

### isNotNull
``` java{2}
isNotNull(R column)
isNotNull(boolean condition, R column)
```
- 字段 IS NULL
- 例: `isNotNull("name")`--->`name is not null`

### in
``` java{2}
in(R column, Collection<?> value)
in(boolean condition, R column, Collection<?> value)
```
- 字段 IN (value.get(0), value.get(1), ...)
- 例: `in("age",{1,2,3})`--->`age in (1,2,3)`

``` java{2}
in(R column, Object... values)
in(boolean condition, R column, Object... values)
```
- 字段 IN (v0, v1, ...)
- 例: `in("age", 1, 2, 3)`--->`age in (1,2,3)`

### notIn
``` java{2}
notIn(R column, Collection<?> value)
notIn(boolean condition, R column, Collection<?> value)
```
- 字段 IN (value.get(0), value.get(1), ...)
- 例: `notIn("age",{1,2,3})`--->`age not in (1,2,3)`

``` java{2}
notIn(R column, Object... values)
notIn(boolean condition, R column, Object... values)
```
- 字段 NOT IN (v0, v1, ...)
- 例: `notIn("age", 1, 2, 3)`--->`age not in (1,2,3)`

### inSql
``` java{2}
inSql(R column, String inValue)
inSql(boolean condition, R column, String inValue)
```
- 字段 IN ( sql语句 )
- 例: `inSql("age", "1,2,3,4,5,6")`--->`age in (1,2,3,4,5,6)`
- 例: `inSql("id", "select id from table where id < 3")`--->`id in (select id from table where id < 3)`

### notInSql
``` java{2}
notInSql(R column, String inValue)
notInSql(boolean condition, R column, String inValue)
```
- 字段 NOT IN ( sql语句 )
- 例: `notInSql("age", "1,2,3,4,5,6")`--->`age not in (1,2,3,4,5,6)`
- 例: `notInSql("id", "select id from table where id < 3")`--->`age not in (select id from table where id < 3)`

### groupBy
``` java{2}
groupBy(R... columns)
groupBy(boolean condition, R... columns)
```
- 分组：GROUP BY 字段, ...
- 例: `groupBy("id", "name")`--->`group by id,name`

### orderByAsc
``` java{2}
orderByAsc(R... columns)
orderByAsc(boolean condition, R... columns)
```
- 排序：ORDER BY 字段, ... ASC
- 例: `orderByAsc("id", "name")`--->`order by id ASC,name ASC`

### orderByDesc
``` java{2}
orderByDesc(R... columns)
orderByDesc(boolean condition, R... columns)
```
- 排序：ORDER BY 字段, ... DESC
- 例: `orderByDesc("id", "name")`--->`order by id DESC,name DESC`

### orderBy
``` java{1}
orderBy(boolean condition, boolean isAsc, R... columns)
```
- 排序：ORDER BY 字段, ...
- 例: `orderBy(true, true, "id", "name")`--->`order by id ASC,name ASC`

### having
``` java{2}
having(String sqlHaving, Object... params)
having(boolean condition, String sqlHaving, Object... params)
```
- HAVING ( sql语句 )
- 例: `having("sum(age) > 10")`--->`having sum(age) > 10`
- 例: `having("sum(age) > {0}", 11)`--->`having sum(age) > 11`

### or
``` java{2}
or()
or(boolean condition)
```
- 拼接 OR
::: tip 注意事项:
主动调用`or`表示紧接着下一个**方法**不是用`and`连接!(不调用`or`则默认为使用`and`连接)
:::
- 例: `eq("id",1).or().eq("name","老王")`--->`id = 1 or name = '老王'`

``` java{2}
or(Function<This, This> func)
or(boolean condition, Function<This, This> func)
```
- OR 嵌套
- 例: `or(i -> i.eq("name", "李白").ne("status", "活着"))`--->`or (name = '李白' and status <> '活着')`

### and
``` java{2}
and(Function<This, This> func)
and(boolean condition, Function<This, This> func)
```
- AND 嵌套
- 例: `and(i -> i.eq("name", "李白").ne("status", "活着"))`--->`and (name = '李白' and status <> '活着')`

### apply
``` java{2}
apply(String sqlHaving, Object... params)
apply(boolean condition, String sqlHaving, Object... params)
```
- 拼接 sql
::: warning 注意事项:
该方法可用于数据库**函数**
动态入参的`params`对应前面`sqlHaving`内部的`{index}`部分.这样是不会有sql注入风险的,反之会有!
:::
- 例: `apply("id = 1")`--->`having sum(age) > 10`
- 例: `apply("date_format(dateColumn,'%Y-%m-%d') = '2008-08-08'")`--->`date_format(dateColumn,'%Y-%m-%d') = '2008-08-08'")`
- 例: `apply("date_format(dateColumn,'%Y-%m-%d') = {0}", "2008-08-08")`--->`date_format(dateColumn,'%Y-%m-%d') = '2008-08-08'")`

### last
``` java{2}
last(String lastSql)
last(boolean condition, String lastSql)
```
- 无视优化规则直接拼接到 sql 的最后
::: tip 注意事项:
只能调用一次,多次调用以最后一次为准
有sql注入的风险,请谨慎使用
:::
- 例: `last("limit 1")`

### exists
``` java{2}
exists(String existsSql)
exists(boolean condition, String existsSql)
```
- 拼接 EXISTS ( sql语句 )
- 例: `exists("select id from table where age = 1")`--->`exists (select id from table where age = 1)`

### notExists
``` java{2}
notExists(String existsSql)
notExists(boolean condition, String existsSql)
```
- 拼接 NOT EXISTS ( sql语句 )
- 例: `notExists("select id from table where age = 1")`--->`not exists (select id from table where age = 1)`

### nested

``` java{2}
nested(Function<This, This> func)
nested(boolean condition, Function<This, This> func)
```
- 正常嵌套 不带 AND 或者 OR
- 例: `nested(i -> i.eq("name", "李白").ne("status", "活着"))`--->`(name = '李白' and status <> '活着')`

## QueryWrapper
::: tip 说明:
继承自 AbstractWrapper ,自身的内部属性 entity 也用于生成 where 条件  
及 LambdaQueryWrapper, 可以通过 new QueryWrapper().lambda() 方法获取
:::

### select
```java{3}
select(String... sqlSelect)
select(Predicate<TableFieldInfo> predicate)
select(Class<T> entityClass, Predicate<TableFieldInfo> predicate)
```
- 设置查询字段
::: tip 说明:
以上方分法为两类.  
第二类方法为:过滤查询字段(主键除外),入参不包含 class 的调用前需要`wrapper`内的`entity`属性有值!
这两类方法重复调用以最后一次为准  
:::
- 例: `select("id", "name", "age")`
- 例: `select(i -> i.getProperty().startsWith("test"))`

### excludeColumns <Badge text="@Deprecated" type="error"/>
- 排除查询字段
::: warning 
已从`3.0.5`版本上移除此方法!
:::

## UpdateWrapper
::: tip 说明:
继承自 `AbstractWrapper` ,自身的内部属性 `entity` 也用于生成 where 条件  
及 `LambdaUpdateWrapper`, 可以通过 `new UpdateWrapper().lambda()` 方法获取!
:::

### set
```java{2}
set(String column, Object val)
set(boolean condition, String column, Object val)
```
- SQL SET 字段
- 例: `set("name", "老李头")`
- 例: `set("name", "")`--->数据库字段值变为**空字符串**
- 例: `set("name", null)`--->数据库字段值变为`null`
### setSql
``` java
setSql(String sql)
```
- 设置 SET 部分 SQL
- 例: `set("name = '老李头')`

### lambda
- 获取 `LambdaWrapper`  
在`QueryWrapper`中是获取`LambdaQueryWrapper`  
在`UpdateWrapper`中是获取`LambdaUpdateWrapper`

## 使用 Wrapper 自定义SQL
::: tip 需求来源:
在使用了`mybatis-plus`之后, 自定义SQL的同时也想使用`Wrapper`的便利应该怎么办？
在`mybatis-plus`版本`3.0.7`得到了完美解决
版本需要大于或等于`3.0.7`, 以下两种方案取其一即可
:::

### Service.java
```java
mysqlMapper.getAll(Wrappers.lambdaQuery(new MysqlData()).eq(MysqlData::getGroup, 1));
```

### 方案一 注解方式 Mapper.java
``` java
@Select("select * from mysql_data ${ew.customSqlSegment}")
List<MysqlData> getAll(@Param(Constants.WRAPPER) Wrapper wrapper);
```

### 方案二 XML形式 Mapper.xml
``` xml
<select id="getAll" resultType="MysqlData">
	SELECT * FROM mysql_data ${ew.customSqlSegment}
</select>

```