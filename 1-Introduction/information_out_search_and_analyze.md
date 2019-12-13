# 信息输出：查询和分析

虽然您能使用 Elasticsearch 存储文档且能检索文档及其元数据，但真正强大的是能够轻松访问构建在 Apache Lucene 搜索引擎库上的全套搜索功能。

Elasticsearch 提供了一个简单的 REST API，用于管理集群、索引和搜索。出于测试目的，你可以轻松地直接从命令行或通过 Kinaba 开发控制台提交提交请求。应用程序可以使用 [Elasticsearch Client](https://www.elastic.co/guide/en/elasticsearch/client/index.html) 进行请求，支持以下语言：`Java`、`JavaScript`、`Go`、`.NET`、`PHP`、`Perl`、`Python`或`Ruby`。

## 查询

Elasticsearch REST APIs 支持结构化查询、全文查询及将二者结合的复杂查询。结构化查询类似于SQL。例如，您可以在`employee`索引中搜索`gender`和`age`字段，并按`hire_date`字段排序。全文查询从所有文档中查询匹配的字符串并返回，最后按`relevance`（与查询字符串的匹配度）进行排序。

除了精确查询外，您还可以进行模糊搜索、相似度搜索和前缀搜索，并得到自动评分。

地理空间或其他数字数据是否也能搜索？Elasticsearch 在优化数据结构中索引非文本数据也支持高性能的地理和数据查询。

您可以使用 Elasticsearch 的综合 JSON 风格的查询语言（DSL）访问这些搜索功能。您还可以构建 SQL 风格的查询，以便在 Elasticsearch 内部本地搜索和聚合数据，JDBC 和 ODBC 驱动程序使范围广泛的第三方应用程序能够通过 SQL 与 Elasticsearch 交互。

## 分析


