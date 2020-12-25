
### 0914

- 数据的价值  
  获得知识，辅助决策（数据驱动运营），驱动业务，预测未来

- 数据库四要素  
  数据库（有组织可共享的数据集合），硬件，软件（DBMS），人（DBA，开发人员，设计人员，用户）

- Mysql体系架构  
  connector（驱动），connection pool（连接池），sql interface（sql语言接口），parser（sql语法解析器），optimizer（优化器）
  caches（缓存），storage engines（存储引擎ISAM InnoBD Memory），enterprise management services

- Mysql内存模型

- Oracle体系架构

- Oracle内存模型

- Oracle数据库对象  
  [表空间] - [表，索引，视图] - [数据段，索引段，临时段，回滚段] - [数据区间] - [数据块]

- SQL执行过程  
  语法分析 -> 语义分析 -> 解析视图 -> 查询优化 -> 生成执行计划 -> 执行语句 -> 返回结果

- 统计信息  
  索引，表，列，CPU&IO


### 1015

- 何时性能调优
  上线前（基于经验的基本参数优化，基于问题），上线后（系统监控）

- 性能调优方式
  网络调优，服务器存储调优，操作系统调优，中间件调优，数据库调优，应用程序调优，架构设计调优

- 参与者
  需求、设计、开发、测试、运维等诸多环节，需要整个团队共同参与，协同解决性能问题

- 优先级
  系统架构，应用代码，中间件，数据库，服务器，存储器，网络

- 表连接方式
  HASH JOIN， NESTED LOOP, SORT MERGE JOIN

- 数据访问方式
  TABLE ACCESS FULL， TABLE ACCESS BY INDEX， INDEX * SCAN

- 重要的系统视图
  V$SESSION, V$SQLAREA, V$SQL_PLAN
  DBA_HIST_ACTIVE_SESS_HISTORY, DBA_HIST_SQLTEXT, DBA_HIST_SQLSTAT
