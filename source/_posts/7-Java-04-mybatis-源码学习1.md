---
title: mybatis源码学习1
comments: true
date: 2019-09-01 20:30
categories: [Java]
tags: [mybatis]
---

> 基于 mybatis version: 3.5.6
## mybatis 架构层级

接口：
SqlSession
Transaction
Executor



MappedStatement
 ｜-- java.sql.Statement


session
 |-- Configuration
 |-- SqlSession


executor
 |-- statement
 |-- resultset
 |     |-- DefaultResultSetHandler
 |

mapping
 |-- BoundSql
 |-- 

## mybatis 设计模式

- `SqlSession` 工厂方法模式
![SqlSession-类图](http://www.plantuml.com/plantuml/png/ZP31YiCW48RlFiKS4nPyWLr2O9Uzzf2yWDX947HCTQIKa7SlGY61QMcFwlVxVpDzPfEu1AySCQR9M8JXuWVCsKLQ5G1y_OmTZ93He-KJOTD-gqqzvV-DvPXkDIPJwrPZrfkSuGydKpplIN1XyHYGE8l-0nKNfFrOJIhm6sboSqc6ApCZ7oyg9Og5yh5VFBxLM45h3Gcv90B2g_oYQIkoVQBxMDHBKwqxEORgaSub-3i0)

`SqlSession`和`SqlSessionFactory` 接口提供默认的Default实现类

<details>
<summary>SqlSession-plantuml</summary>
@startuml
interface SqlSession{
   +<T> T selectOne();
}

interface SqlSessionFactory{
  +<T> T selectOne(){}
}

class DefaultSqlSession implements SqlSession{
   +SqlSession openSqlSession();
   +Configuration getConfiguration();
}

class DefaultSqlSessionFactory implements SqlSessionFactory{
   +SqlSession openSqlSession(){ ... return new SqlSessionFactory() ...}
   +Configuration getConfiguration(){}
}

DefaultSqlSessionFactory ..> DefaultSqlSession
@enduml
</details>

Transaction 也是同样的
`class JdbcTransaction implements Transaction`
`class JdbcTransactionFactory implements TransactionFactory`

- `SqlSessionFactoryBuilder` 
提供多个不同参数重载 `build`方法创建 SqlSessionFactory


- `Executor` 模版方法模式
![Executor-类图](http://www.plantuml.com/plantuml/png/lOz1oi8m48NtESN0l_d58xWf1G-WrWDCan4BIHEJ2LHAxsw3rKQfs-uIUFE-xnM1qNCqMZGjax-W9DXt92DRtmk0xIsIl_zlTkaTdKAcz1c4m3gmHyaWDOO09GPw7K9Zd2P3BOVFCXThtKYeO6hjGTd3180XIumoCDC4m9_pbaaoANY3g-pwHoJrA7lElP-wfwGu2rF7rAgl5Rdo4hJFZPUHAJDCo19PNbSb7Yc6jJOMOtSq-W40)

<details>
<summary>Executor-plantuml</summary>
@startuml
interface Executor{
  +query();
  +update();
}

abstract class BaseExecutor implements Executor{
  +public T query(){ doQuery(); }
  +public int update();

  #protected abstract T doQuery();
  #protected abstract int doUpdate();
}

class SimpleExecutor extends BaseExecutor {
  +public abstract T doQuery(){}
  +public abstract int doUpdate(){}
}
class BatchExecutor extends BaseExecutor {
  +public abstract T doQuery(){}
  +public abstract int doUpdate(){}
}
class ReuseExecutor extends BaseExecutor {
  +public abstract T doQuery(){}
  +public abstract int doUpdate(){}
}
@enduml
</details>



## 源码浅析
Test.java源码
```java
public class Test {
  public static void main(String[] args) throws Exception {
    Class.forName("com.mysql.cj.jdbc.Driver");
    String resource = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource); // 0.
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream); // 1.

    try (SqlSession session = sqlSessionFactory.openSession()) { // 2.
      // one way
      Blog blog = session.selectOne("com.stan.mybatis.domain.blog.mappers.BlogMapper.selectBlog", 1);  // 3.
      System.out.println(blog);

      // other way
      BlogMapper blogMapper = session.getMapper(BlogMapper.class); // 4.
      Blog blog2 = blogMapper.selectBlog(2); // 5.
      System.out.println(blog2);
    } catch (Exception e) {
      e.printStackTrace();
    }
  }
}
```

1. 第一步 `new SqlSessionFactoryBuilder().build()` 返回的是 `DefaultSqlSessionFactory` 的一个实例对象
`sqlSessionFactory`对象初始化了 Configuration, 主要是将xml配置的 Environment DataSource 进行初始化

这里build 会通过`XMLConfigBuilder` 调用 `XMLMapperBuilder::buildStatementFromContext`，最终调用`Configuration::addMappedStatement` 将`MappedStatement`以 id=>MappedStatement 的形式保存到`Configuration.mappedStatements`属性中，备后面使用
详细调用栈：
```log
addMappedStatement:768, Configuration (org.apache.ibatis.session)
addMappedStatement:297, MapperBuilderAssistant (org.apache.ibatis.builder)
parseStatementNode:113, XMLStatementBuilder (org.apache.ibatis.builder.xml)
buildStatementFromContext:138, XMLMapperBuilder (org.apache.ibatis.builder.xml)
buildStatementFromContext:131, XMLMapperBuilder (org.apache.ibatis.builder.xml)
configurationElement:121, XMLMapperBuilder (org.apache.ibatis.builder.xml)
parse:95, XMLMapperBuilder (org.apache.ibatis.builder.xml)
mapperElement:377, XMLConfigBuilder (org.apache.ibatis.builder.xml)
parseConfiguration:120, XMLConfigBuilder (org.apache.ibatis.builder.xml)
parse:99, XMLConfigBuilder (org.apache.ibatis.builder.xml)
build:78, SqlSessionFactoryBuilder (org.apache.ibatis.session)
build:64, SqlSessionFactoryBuilder (org.apache.ibatis.session)
main:27, Test (com.stan.mybatis) 
```

2. sqlSessionFactory.openSession() 无参openSession方法使用默认的 ExecutorType.SIMPLE
 - 调用 DefaultSqlSessionFactory.openSessionFromDataSource() 方法, 该方法 `new DefaultSqlSession(configuration, executor, autoCommit)` 得到 DefaultSqlSession 的实例对象

- DefaultSqlSessionFactory.openSessionFromDataSource 源码
```java
// 默认 execType=SIMPLE,level=null,autoCommit=false
private SqlSession openSessionFromDataSource(ExecutorType execType, TransactionIsolationLevel level, boolean autoCommit) {
    Transaction tx = null;
    try {
      final Environment environment = configuration.getEnvironment();
      // 这里的transactionFactory 返回的是 JdbcTransactionFactory 的实例
      final TransactionFactory transactionFactory = getTransactionFactoryFromEnvironment(environment);
      // 这里返回 JdbcTransaction 的实例,此时 JdbcTransaction的Connection 为空
      tx = transactionFactory.newTransaction(environment.getDataSource(), level, autoCommit);

      /**
       * 这里的 configuration 为 org.apache.ibatis.session.Configuration
       * 这个类为什么设计得这么臃肿？ newExecutor() 方法也在其中...
       */
      final Executor executor = configuration.newExecutor(tx, execType);
      // 构造DefaultSqlSession是传入configuration和executor，内部接收赋值到私有属性
      return new DefaultSqlSession(configuration, executor, autoCommit);
    } catch (Exception e) {
      closeTransaction(tx); // may have fetched a connection so lets call close()
      throw ExceptionFactory.wrapException("Error opening session.  Cause: " + e, e);
    } finally {
      ErrorContext.instance().reset();
    }
  }
```

- JdbcTransactionFactory 源码
```java
public class JdbcTransactionFactory implements TransactionFactory {
  @Override
  public Transaction newTransaction(Connection conn) {
    return new JdbcTransaction(conn);
  }
  @Override
  public Transaction newTransaction(DataSource ds, TransactionIsolationLevel level, boolean autoCommit) {
    return new JdbcTransaction(ds, level, autoCommit);
  }
}
```

3. `DefaultSqlSession` 实例调用 `selectOne()`
实际是调用的 `selectList` 再返回 `get(0)`，如果sql 返回的结果多于1条则会抛出异常`TooManyResultsException`
调用`selectList(String statement, Object parameter)`时，会自动加上参数 RowBounds.DEFAULT, RowBounds.DEFAULT=new RowBounds();
RowBounds默认构造方法为：
```java
  public RowBounds() {
    this.offset = 0;
    this.limit = Integer.MAX_VALUE;
  }
```

- DefaultSqlSession::selectList 源码:
```java
  @Override
  public <E> List<E> selectList(String statement, Object parameter, RowBounds rowBounds) {
    try {
      MappedStatement ms = configuration.getMappedStatement(statement);
      return executor.query(ms, wrapCollection(parameter), rowBounds, Executor.NO_RESULT_HANDLER);
    } catch (Exception e) {
      throw ExceptionFactory.wrapException("Error querying database.  Cause: " + e, e);
    } finally {
      ErrorContext.instance().reset();
    }
  }
```

- configuration.getMappedStatement 源码:
```java
  public MappedStatement getMappedStatement(String id, boolean validateIncompleteStatements) {
    // 这个参数默认为 true
    if (validateIncompleteStatements) {
      // 本次调试这个方法里边什么也没有做？？？
      buildAllStatements();
    }
    // 这里直接返回 mappedStatements 中保存的id
    return mappedStatements.get(id);
  }
```
`mappedStatements` 是一个 StrictMap, `StrictMap<V> extends HashMap<String, V>`, 重写了 put,get方法。  
这里的`get(id)`直接返回第1步中保存下来的 MappedStatement。


- `executor.query` 最后调用的是 queryFromDatabase
BaseExecutor::queryFromDatabase 源码：
```java
  private <E> List<E> queryFromDatabase(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, CacheKey key, BoundSql boundSql) throws SQLException {
    List<E> list;
    // TODO: 这里缓存处理的目的是什么？
    localCache.putObject(key, EXECUTION_PLACEHOLDER);
    try {
      // 这里调用子类 SimpleExecutor 的doQuery方法(CachingExecutor 委派 delegate)
      list = doQuery(ms, parameter, rowBounds, resultHandler, boundSql);
    } finally {
      localCache.removeObject(key);
    }
    localCache.putObject(key, list);
    if (ms.getStatementType() == StatementType.CALLABLE) {
      // TODO: 缓存parameter 是什么目的？
      localOutputParameterCache.putObject(key, parameter);
    }
    return list;
  }
```

- SimpleExecutor::doQuery 源码：
```java
  @Override
  public <E> List<E> doQuery(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, BoundSql boundSql) throws SQLException {
    Statement stmt = null;
    try {
      Configuration configuration = ms.getConfiguration();
      // 这里new出来的是RoutingStatementHandler，委派属性delegate为PreparedStatementHandler
      StatementHandler handler = configuration.newStatementHandler(wrapper, ms, parameter, rowBounds, resultHandler, boundSql);
      // 得到的stmt 是 com.mysql.cj.jdbc.ClientPreparedStatement 的实例
      stmt = prepareStatement(handler, ms.getStatementLog());
      // 调用的是 ClientPreparedStatement::execute()
      return handler.query(stmt, resultHandler);
    } finally {
      closeStatement(stmt);
    }
  }
```






## 待解惑

- abstract BaseExecutor 构造函数中 this.wrapper = this; 这个构造是由子类调用的，这里保存 wrapper是出于什么原因？

- Configuration 为什么设计得这么臃肿？
官方文档描述： 
> The Configuration class contains everything you could possibly need to know about a SqlSessionFactory instance.

- Configuration 默认的cacheEnabled=true, 所以这里会创建 CachingExecutor
```java
  if (cacheEnabled) {
    executor = new CachingExecutor(executor);
  }
```

- 关于插件如何使用的？为什么会调用 `executor = (Executor) interceptorChain.pluginAll(executor);`

- skipRows 是怎么回事，不是物理分页吗？
```java
  private void skipRows(ResultSet rs, RowBounds rowBounds) throws SQLException {
    if (rs.getType() != ResultSet.TYPE_FORWARD_ONLY) {
      if (rowBounds.getOffset() != RowBounds.NO_ROW_OFFSET) {
        rs.absolute(rowBounds.getOffset());
      }
    } else {
      for (int i = 0; i < rowBounds.getOffset(); i++) {
        if (!rs.next()) {
          break;
        }
      }
    }
  }
```

