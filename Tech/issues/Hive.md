# Hive
Hadoop生态系统的挑战？
用户如何从一个现有的数据基础架构转移到Hadoop上(现有的基础架构是基于传统关系型数据库和结构化查询语句SQL的)？
这就是Hive出现的原因。Hive提供了一个被称为Hive查询语言(简称HiveQL或HQL)的SQL方言，来查询存储在Hadoop集群中的数据。

SQL知识分布广泛的一个原因是：它是一个可以有效的、合理的、直观的组织和使用数据模型。即使对于经验丰富的Java开发工程师来说，将这些常见的数据运算对应到底层的MapReduce Java API也是令人畏缩的。Hive可以帮助用户来做这些苦活，这样用户就可以集中精力关注于查询本身了。

Hive可以将大多数的查询SQL语句转换为MapReduce任务，进而在介绍一个令人熟悉的SQL抽象的同时，拓宽Hadoop的可扩展性。

然而！！
基于Hadoop以及HDFS的设计本身的约束和局限性，限制了Hive所能胜任的工作：
- Hive不支持记录级别的更新、插入或者删除操作，但是用户可以通过查询生成新表或者将查询结果导入到文件中。
- 因为Hadoop是一个面向批处理的系统，而MapReduce任务(job)的启动过程需要消耗较长的时间，所以Hive查询延时比较严重(传统数据库中在秒级别可以完成的查询，在Hive中，即使数据集相对较小，往往也需要执行更长的时间)
- Hive不支持事务。Hive不支持OLTP(联机事务处理)所需的关键功能，而更接近成为一个OLAP(联机分析技术)工具。
- Hive适合数据仓库应用程序，可以维护海量数据，进行数据挖掘，形成一件和报告(不要求时效性)。
- HiveQL不符合ANSI SQL标准，但与MySQL提供的SQL方言最接近。

### HiveQL数据定义
Hive创建数据库：
```bash
hive> CREATE DATABASE financials;
```
查看Hive中所包含的数据库：
```bash
hive> SHOW DATABASES;
default
financials

hive> CREATE DATABASE human_resources;

hive> SHOW DATABASES;
default
financials
human_resources
```
如果数据库非常多的话，那么可以使用正则表达式匹配来筛选出需要的数据库名：
```bash
hive> SHOW DATABASES LIKE 'h.*';
human_resources
hive> ...
```
Hive会为每个数据库创建一个目录，数据库中的表将会以这个数据库目录的子目录形式存储(例外是default数据库中的表，这个数据库本身没有自己的目录)。
数据库所在的目录位于属性*hive.metastore.warehouse.dir*所指定的顶层目录之后，假设用户使用的配置是*/user/hive/warehouse*，那么当我们创建数据库financials时，Hive将会对应地创建一个目录*/user/hive/warehouse/financials.db*。
用户可以通过如下的命令来修改这个默认的位置：
```bash
hive> CREATE DATABASE financials
    > LOCATION '/my/preferred/directory';
```
用户也可以为这个数据库增加一个描述信息，通过DESCRIBE DATABASE <database>命令就可以查看到该信息。
```bash
hive> CREATE DATABASE financials
    > COMMENT 'Holds all financial tables';

hive> DESCRIBE DATABASE financials;
financials Holds all financial tables
  hdfs://master-server/user/hive/warehouse/financials.db
```


### Hive 接收Streaming

#技术笔记/大数据