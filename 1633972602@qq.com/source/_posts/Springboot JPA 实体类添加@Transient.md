---
title: Springboot JPA 实体类添加@Transient
categories: Java
tags: java, springboot
---

* Hibernate
> 在实体类与数据库表建立映射关系时添加 ***@Table*** 注解, 当表中不存在实体类中的某个属性的时候, 就需要用到 **@Transient** 注解

* 用法: 添加在需要忽略的实体类属性上
> ***@Transient***
>  private String columeName;

* 实体类
```
import com.fasterxml.jackson.annotation.JsonIgnore;
import jdk.nashorn.internal.ir.annotations.Ignore;
import lombok.Data;
import javax.persistence.*;

@lombok.Data
@Entity
public class UserMonitorStationMapping {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;
    
    private Integer monitorStationId;
    
    @Transient
    private String shortName;        // TODO 数据库表中无此字段

    @ManyToOne
    @JsonIgnore
    private User user;
}
```
* 数据库表结构
![这里写图片描述](https://img-blog.csdn.net/20180720163211969?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


> org.hibernate.exception.SQLGrammarException: could not extract ResultSet
at org.hibernate.exception.internal.SQLExceptionTypeDelegate.convert(SQLExceptionTypeDelegate.java:82)
at org.hibernate.exception.internal.StandardSQLExceptionConverter.convert(StandardSQLExceptionConverter
.java:49)at org.hibernate.engine.jdbc.spi.SqlExceptionHelper.convert(SqlExceptionHelper.java:124)
at org.hibernate.engine.jdbc.spi.SqlExceptionHelper.convert(SqlExceptionHelper.java:109)
at org.hibernate.engine.jdbc.internal.ResultSetReturnImpl.extract(ResultSetReturnImpl.java:88)
at org.hibernate.loader.Loader.getResultSet(Loader.java:2062)
at org.hibernate.loader.Loader.executeQueryStatement(Loader.java:1859)
at org.hibernate.loader.Loader.executeQueryStatement(Loader.java:1838)
at org.hibernate.loader.Loader.doQuery(Loader.java:906)
at org.hibernate.loader.Loader.doQueryAndInitializeNonLazyCollections(Loader.java:348)
at org.hibernate.loader.Loader.doList(Loader.java:2550)
at org.hibernate.loader.Loader.doList(Loader.java:2536)
at org.hibernate.loader.Loader.listIgnoreQueryCache(Loader.java:2366)
at org.hibernate.loader.Loader.list(Loader.java:2361)
at org.hibernate.loader.hql.QueryLoader.list(QueryLoader.java:495)
at org.hibernate.hql.internal.ast.QueryTranslatorImpl.list(QueryTranslatorImpl.java:357)
at org.hibernate.engine.query.spi.HQLQueryPlan.performList(HQLQueryPlan.java:198)
at org.hibernate.internal.SessionImpl.list(SessionImpl.java:1230)
at org.hibernate.internal.QueryImpl.list(QueryImpl.java:101)
at com.bbs.dao.BaseDao.list(BaseDao.java:82)
at com.bbs.dao.BaseDao.list(BaseDao.java:92)
at com.bbs.service.impl.OrderServiceImpl.selectOrdersByOne(OrderServiceImpl.java:35)
at com.bbs.service.impl.OrderServiceImpl

* 解决方法
> 在实体类上添加 ***@Transient*** 注解
