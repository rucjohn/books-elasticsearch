# `node.name`

Elasticsearch 使用 `node.name` 作为 Elasticsearch 节点的可读标识符，因此它包含在许多 API 的响应中。不配置时默认为 Elasticsearch 程序启动时的服务器主机名，可以在 `elasticsearch.yml` 中显式配置；如下所示：
```
node.name: prod-data-2
```
