---

title: BigQuery 入门
categories: Coding
tags: 
  - machine-learning
date: 2020-03-08 19:33:39

---

在 2020 年的今天，如果没有大量的数据存储，机器学习就是一场空谈。当我们谈论大数据量存储的时候，我们就会考虑选择怎么样的基础架构才能降低大量数据存储所带来的耗时以及费用昂贵的问题。Google 推出了一种全托管式云数据仓库 -- BigQuery

<!--more-->

# What's BigQuery?

先来看看[官方文档](https://cloud.google.com/bigquery/docs)的中英文简介：

> BigQuery is Google's fully managed, petabyte scale, low cost analytics data warehouse. BigQuery is NoOps—there is no infrastructure to manage and you don't need a database administrator—so you can focus on analyzing data to find meaningful insights, use familiar SQL, and take advantage of our pay-as-you-go model.
> 
> BigQuery 是 Google 推出的全托管式低成本分析数据仓库，可支持 PB 级数据规模。BigQuery 无需运维：没有需要管理的基础架构，也无需指派数据库管理员。因此，您可以专注于分析数据，使用熟悉的 SQL 发掘有意义的数据洞见，并利用我们的即用即付模式。

由以上简介的内容来看，BigQuery 可以解释为存放于 Google Cloud 的企业级数据仓库，你可以通过 Google 基础的强大处理能力实现**针对数据集中的大量数据进行快速 SQL 查询**。所以，使用 BigQuery 的交互方式主要是有以下三种：

* 加载和导出数据
* 查询数据
* 管理数据

而对于如何进行上述的数据交互，Google 提供了 4 种方式：

* Google Cloud Console 中的 [BigQuery 网页界面](https://cloud.google.com/bigquery/docs/bigquery-web-ui)
* BigQuery [经典版网页界面](https://cloud.google.com/bigquery/docs/quickstarts/quickstart-web-ui-classic)
* BigQuery [命令行工具](https://cloud.google.com/bigquery/docs/quickstarts/quickstart-command-line)
* BigQuery [REST API](https://cloud.google.com/bigquery/docs/reference/rest)

# The structure of BigQuery

在学习与 BigQuery 的数据交互方式之前，先来了解一下 BigQuery 内部的基础架构。

## Datasets

Datasets 是管理和控制 tables 和 views 的顶级容器, 包含在特定的项目中。tables 或者 views 必须属于 Datasets, 因此在加载数据到 BigQuery 中，至少需要创建一个数据集。而创建数据集时需要注意以下几点：

* 创建数据集时必须指定一个位置(参考 [Datasets Loction](https://cloud.google.com/bigquery/docs/locations))，而位置一但确定之后，没有办法更改，但是可以[复制 Dataset 到其他区域](https://cloud.google.com/bigquery/docs/copying-datasets)。
* 每个项目中的 Datasets 名字必须是唯一的。
* Query 中引用的 tables 必须是同一位置数据集中的表
* 复制 table 时，源表和目标表的数据集必须在同一个位置。

最重要的是，**创建、更新或者删除数据集是不需要付费的** :p。

关于如何创建数据集，控制数据集的访问，Google 提供了详细的[文档](https://cloud.google.com/bigquery/docs/datasets)

## Tables

BigQuery 允许在创建 table 时指定 table schema, 或者先创建表，之后在首次加载数据时使用 RESI API 指定 schema。

BigQuery 还对支持的数据格式提供自动检测的功能为 table 创建 schema加载 Avro、Parquet、ORC、Firestore 导出数据或 Datastore 导出数据时，BigQuery 会自动从数据文件中检索架构。

对于 BigQuery 表的操作，有几点限制：

* 每个 Datasets 的 table name 必须唯一
* 界面化操作只支持一个 table 的复制与删除
* 复制 table 时，目标 dataset 必须与源表在同一位置
* 复制多个 table 到一个目标 table 时，源表的 schema 必须一致 
* 导出 table 数据的唯一支持的目标存储是 Google cloud storage

Table 在 BigQuery 中的费用取决于存储的数据量以及对数据运行的查询，而对于创建表，复制表以及导出表里的数据是不收取费用的。

### Schema

除了在使用文件导入数据时可以自动检测 schema 之外，还有几种指定 table schema 的方式:

* 手动使用 UI 界面 (Google Cloud Console）
* 创建 JSON 格式的 schema 文件, 之后将 JSON 文件做为 CLI 创建 table 命令时的配置参数
* 调用 REST API 	`jobs.insert` 或者 `tables.insert` 中使用 `schema` 属性进行配置

在指定 table schema 的时候必须提供每个列的 **name** 和 **data type**, **description** 和 **mode** 是可选项

* **Names**: 列名只能包含字母（a-z、A-Z）、数字 (0-9) 或下划线 (_)，并且必须以字母或下划线开头。列名长度不得超过 128 个字符。列名不能重复，并且大小写不敏感。
* **Data type**: 支持标准 SQL 数据类型 -- [参考文档](https://cloud.google.com/bigquery/docs/schemas#standard_sql_data_types)
* Description: 可选项，类型为字符串，长度最大为 1024 个字符
* Mode: 支持 Nullable, Required, Repeated（包含[指定类型值的数组](https://cloud.google.com/bigquery/docs/nested-repeated)）, 可选项，默认是Nullable

关于几种指定 shcema 方式的具体操作和修改表的 schma，参考 [BigQuery 文档](https://cloud.google.com/bigquery/docs/schemas)

###  Partitioned tables

Partitioned tables (分区表) 是**一种将表划分为多个区段的特殊表**。 分区表与常规表相比有两点优势：

* 可以通过将大型表划分为较小的分区来**提高查询的性能**
* 通过查询读取的字节数，来**控制费用**

BigQuery table 有三种分区方式。

#### Ingestion-time partitioned table

当选择 [Ingestion time 分区表](https://cloud.google.com/bigquery/docs/creating-partitioned-tables)时， BigQuery 会**自动按照数据提取或者到达的日期进行分区**。Ingestion-time 分区表包含一个名为 `_PARTITIONTIME`, 这个伪列中存储着数据加载日期的时间戳，我们可以通过修改 `_PARTITIONTIME` 列的值来重定向数据到别的分区。

对于这类分区表的查询可以使用 `_PARTITIONTIME` 列来指定过滤条件，以减少查询扫描的数据量。

#### Date or timestamp partitioned tables

BigQuery 允许使用**特定的 Date 列或者 timestamp 列进行分区**，写入 [Date/timestamp 分区表](https://cloud.google.com/bigquery/docs/creating-column-partitions) 的数据会根据相应列的日期分配到对应的分区。这种分区表可以不使用伪列，可以根据分区列指定过滤条件。

#### Integer range partitioned tables （beta）

BigQuery 也允许使用**特定的 Integer 列，根据提供的start，end以及 interval 来分区**。如果需要创建 [Integer-range 分区表](https://cloud.google.com/bigquery/docs/creating-integer-range-partitions)，需要提供以下各项配置：

* 用于创建 integer range 分区的列名
* 分区范围的 start 值（含边界值）
* 分区范围的 end 值（不含边界值）
* 分区中每个范围的阈值 interval

**PS**: start 和 end 之间范围的数量上限可能为 10 万。但是，由于每个范围都是一个分区，因此每个表的数据范围数量上限为 4000 个分区。

对于 Date/timestamp 分区表 和 Integer-range 分区表来说，会存在两个特殊的分区：

* `__NULL__` 分区：表示分区列中具有 NULL 值的行
* `__UNPARTITIONED__` 分区：表示在允许范围之外的数据

关于如何创建，管理，查询  partition table, 参考 [BigQuery 文档](https://cloud.google.com/bigquery/docs/partitioned-tables)

## Clustered tables - WIP
## Views - WIP

# Working with data in BigQuery

## Loading data into BigQuery

BigQuery 本身也支持不用加载就可以执行的查询，但是限制较多，很多情况下还是需要将数据加载到 BigQuery 中才能进行查询。加载数据的方式有很多种：

* 从 Google Cloud Storage 加载
* 从其他 Google 服务（例如 Google Ad Manager 和 Google Ads）加载
* 从本地数据源加载
* 使用[流式插入](https://cloud.google.com/bigquery/streaming-data-into-bigquery)功能插入各条记录
* 使用 [DML(Data Manipulation Language)]() 语句执行批量插入
* 在 Dataflow 流水线中使用 [BigQuery I/O](https://beam.apache.org/documentation/io/built-in/google-bigquery/) 转换将数据写入 BigQuery

**Notes**: 加载数据时可以将数据加载到新的表或分区或者将数据附加到现有的表或分区，也可以覆盖某个表或分区。具体详见 [Manage partitioned tables](https://cloud.google.com/bigquery/docs/managing-partitioned-tables)

目前，对于从文件加载数据我们只支持 Google Cloud Storage 和 本地数据源，并且支持的数据格式只有以下几种：

* Cloud Storage: Avro，CSV， JSON (仅限换行符分隔格式)， ORC，Parquet，Datastore exports，Firestore exports
* 本地数据源: Avro，CSV， JSON (仅限换行符分隔格式)，ORC，Parquet

加载数据时的默认格式是 CSV，并且数据加载时也有相对应的限制，具体参考[BigQuery 文档](https://cloud.google.com/bigquery/docs/loading-data)

## Query data from BigQuery

BigQuery 的查询方式主要有两种：

* **交互式查询**：查询会尽快执行
* **批量查询**：将每个批量查询排成队列，并在 BigQuery 共享资源池中有空闲资源可用时尽快开始查询

### Query dry run
**BigQuery 会根据处理的字节数作为查询费用的指标**，所以 BigQuery 会提供试运行查询返回处理字节数的预估值，之后可以使用价格计算器计算查询费用。试运行查询操作不会收取费用。

除了界面化会直接显示试运行查询结果外，还可以使用以下方式：

* 在 CLI 的 query 命令里使用 `--dry_run` 标志
* API 中使用 `dryRun` 参数

### Query results

BigQuery 的查询结果会全部保存在 table 中，可以是**永久表**，也可以是**临时表**

* **临时表**：是保存在特殊 Datasets 里面的随机命名表，用来缓存查询结果。生命周期大约为 24 个小时
* **永久表**：是有权限访问的任何 Datasets 中的新 table 或者已有的 table, 对于已有的 table，可以追加数据也可以覆盖数据

目前 BigQuery 提供三种下载查询结果的方式：

* 下载到本地文件
* 保存到 Google drive - beta
* 保存到 Google sheets

**Notes**: 如果查询结果保存在临时表里，当执行重复查询的时候，BigQuery 会尝试使用缓存的结果。详细内容查询 [BigQuery 官方文档](https://cloud.google.com/bigquery/docs/cached-results)

### Parameterized queries -- WIP
### Querying multiple tables using a wildcard -- WIP

# CLI - bq

It will come soon.

# Reference

* [BigQuery documentation](https://cloud.google.com/bigquery/docs)



