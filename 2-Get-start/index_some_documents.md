# 索引文档

一旦集群启动运行，就可以为一些数据编制索引。将 JSON 文档放入到 Elasticsearch 索引中。

您可以通过简单的 PUT 请求直接执行此操作，该请求指定要添加文档的索引、唯一的文档 ID，以及请求正文中的一个或多个的`"field: value"`。
```
PUT /customer/_doc/1
{
  "name": "John Doe"
}
```
此请求自动创建索引，如果它不存在，添加一个 ID 为 1 的新文档，并存储和索引名称字段。

由于这是一个新文档，响应显示操作的结果是创建文档，版本为1：
```
{
  "_index" : "customer",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "failed" : 0
  },
  "_seq_no" : 26,
  "_primary_term" : 4
}
```

新文档可以立即从集群中的任何节点获得。您可以使用GET请求并指定文档ID来检索：
```
GET /customer/_doc/1
```

响应指示找到了指定 ID 的文档，并显示已索引的原始源字段。
```
{
  "_index" : "customer",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "_seq_no" : 26,
  "_primary_term" : 4,
  "found" : true,
  "_source" : {
    "name": "John Doe"
  }
}
```

## 批量索引文档

如果有很多文档需要索引，可以使用批量 API 来批量提交。使用批处理文档操作要比单独提交请求快得多，因为它最大限度地减少了网络开销。

最佳批量大小取决于多个因素：文档大小和复杂性、索引和搜索负载，以及集群可用的资源。一次性批处理 1000-5000 个文档，总负载在 5M-15M 之间。你可以尝试找到最适合的值。

要往 Elasticsearch 中录入一些数据，然后开始搜索和分析：

### 1. 下载源数据

下载 [`accounts.json`](https://github.com/elastic/elasticsearch/blob/master/docs/src/test/resources/accounts.json?raw=true) 示例数据集。此随机生成的数据集的文档表示具有以下信息的用户帐户：
```
{
    "account_number": 0,
    "balance": 16623,
    "firstname": "Bradshaw",
    "lastname": "Mckenzie",
    "age": 29,
    "gender": "F",
    "address": "244 Columbus Place",
    "employer": "Euron",
    "email": "bradshawmckenzie@euron.com",
    "city": "Hobucken",
    "state": "CO"
}
```

### 2. 批量请求

使用 `_bulk` 请求批量索引帐户数据到 `bank` 索引中：
```
curl -H "Content-Type: application/json" -XPOST "localhost:9200/bank/_bulk?pretty&refresh" --data-binary "@accounts.json"
curl "localhost:9200/_cat/indices?v"
```

响应提示已成功索引了1000条记录
```
health status index uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   bank  l7sSYV2cQXmu6_4rJWVIww   5   1       1000            0    128.6kb        128.6kb
```

