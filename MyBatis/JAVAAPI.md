#SqlSessions
使用MyBatis的主要接口是`SqlSession`,它由`SqlSessionFactory`实例创建。
`SqlSessionFactory`由`SqlSessionFactoryBuider`创建,它可以从XML,注解或JAVA代码创建`SqlSessionFactory`。

##SqlSessionFactoryBuilder
使用了构造者模式，传入配置，构造出一个`SqlSessionFactory`。

##SqlSessionFactory
构建SqlSessionFactory
`SqlSessionFactory`有多个`openSession()`.
`openSession()`
开启一个事务(不自动提交)；
从当前环境配置的数据源获取Connection对象；
预处理语句不会被复用,不批量处理更新。
`openSession(boolean autoCommit)`
可以来构建自动提交的`sqlSession`
`openSession(Connection connection)`
自定义的数据源

##SqlSession
MyBatis核心类
由以下方法
###执行语句
####用来执行XML文件中定义的CRUD;
```
<T> T selectOne(String statement, Object parameter)
<E> List<E> selectList(String statement, Object parameter)
<K,V> Map<K,V> selectMap(String statement, Object parameter, String mapKey)
int insert(String statement, Object parameter)
int update(String statement, Object parameter)
int delete(String statement, Object parameter)
```
statement是xml命名空间
####同时以上函数还有一套无参版本
```
<T> T selectOne(String statement)
<E> List<E> selectList(String statement)
<K,V> Map<K,V> selectMap(String statement, String mapKey)
int insert(String statement)
int update(String statement)
int delete(String statement)
```
####select还有三个高级方法
运行限制返回行数范围,或提供自定义结果控制逻辑
```
<E> List<E> selectList (String statement, Object parameter, RowBounds rowBounds)
<K,V> Map<K,V> selectMap(String statement, Object parameter, String mapKey, RowBounds rowbounds)
void select (String statement, Object parameter, ResultHandler<T> handler)
void select (String statement, Object parameter, RowBounds rowBounds, ResultHandler<T> handler)
```
#####RowBounds
`RowBounds`参数告诉MyBatis掠过指定数量的记录,还有限制返回的数量。类似于分页查询。
```
int offset = 100;
int limit = 25;
RowBounds rowBounds = new RowBounds(offset, limit);
```
#####ResultHandler
`ResultHandler`是每一行的回调方法，功能类似于stream的forEach
ResultContext用于访问结果对象，创建的对象数据以及stop方法，stop方法停止加载更更多结果。
```
package org.apache.ibatis.session;
public interface ResultHandler<T> {
  void handleResult(ResultContext<? extends T> context);
}
```

###事务方法
force为true去报commit和rollback保证事务被正常处理。
```
void commit()
void commit(boolean force)
void rollback()
void rollback(boolean force)
```

###缓存处理
MyBatisy由两种缓存 `local cache` `second level cache`
####`local cache`
`session`创建后,会创建一个与之关联的`local cache`,session执行过的查询语句本身都会保存到本地缓存中.
`local cache`会被 CUD，提交关闭事务,关闭session 所清空。
默认情况下，本地缓存数据可在整个 session 的周期内使用，这一缓存需要被用来解决循环引用错误和加快重复嵌套查询的速度，所以它可以不被禁用掉，但是你可以设置 localCacheScope=STATEMENT 表示缓存仅在语句执行时有效。
`clearCache()`用来清除本地缓存。
####`second level cache`
映射语句文件中的缓存
通过在映射文件添加`<cache />`开启
>映射语句文件中的所有 select 语句将会被缓存。
映射语句文件中的所有 insert,update 和 delete 语句会刷新缓存。
缓存会使用 Least Recently Used(LRU,最近最少使用的)算法来收回。
根据时间表(比如 no Flush Interval,没有刷新间隔), 缓存不会以任何时间顺序 来刷新。
缓存会存储列表集合或对象(无论查询方法返回什么)的 1024 个引用。
缓存会被视为是 read/write(可读/可写)的缓存,意味着对象检索不是共享的,而 且可以安全地被调用者修改,而不干扰其他调用者或线程所做的潜在修改。

##关闭session
```
void close()
```

##使用映射器
<T> T getMapper(Class<T> type)
XML命名空间使用完全限定名,这个命名可以直接映射到在命名空间中同名的 Mapper 类，并将已映射的 select 语句中的名字、参数和返回类型匹配成方法。
###映射器
一个映射器类就是一个仅需声明与SqlSession方法所匹配的方法的接口。
方法名必须匹配映射语句的 ID。
此外，返回类型必须匹配期望的结果类型，单返回值时为所指定类的值，多返回值时为数组或集合。所有常用的类型都是支持的，包括：原生类 型、Map、POJO 和 JavaBean。
如果传递多个参数给一个映射器方法,默认清空他们以"param"+他们在参数中的位置来命名，如`#{param1},#{param2}`，可以使用`@Param("paramName")`来改变参数名称(多参使用)。
###映射器注解
通过JAVA注解映射器来实现简单映射语句,不推荐。
```


```