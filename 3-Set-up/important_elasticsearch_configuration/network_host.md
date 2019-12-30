# `network.host`

默认情况下，Elasticsearch 仅绑定在回环地址上，如 `127.0.0.1` 和 `[::1]`。这足以在服务器上运行单个节点。

> 提示：实际上，可以从单个节点的相同的 `$ES_HOME` 启动多个节点。这对于测试 Elasticsearch 配置集群能力是有用的，但不推荐在生产环境中使用此配置。

为了与其他服务器上的节点组成集群，您的节点不能绑定在回环地址上。虽然有许多网络设置，但通常只需要配置 `network.host`：
```
network.host: 192.168.1.10
```
或
```
network.host: 0.0.0.0
```

`network.host` 设置还需再了解一些特殊值，如 `_local_`, `_site_`, `_global_` ，以及像 `:ip4` 和 `:ip6` 配置，详细信息可以参考 [Special values for network.host](https://rucjohn.gitbook.io/elasticsearch/5-Aggregations/modules/network_settings)

> 一旦为 `network.host` 提供了自定义设置，Elasticsearch 就会假设您正在从开发模式转到生产模式，并将许多系统启动检查从 warning 升级到 exception。有关详细信息，请参见 [Development mode vs production mode](https://rucjohn.gitbook.io/elasticsearch/3-set-up/important_system_configuration)

