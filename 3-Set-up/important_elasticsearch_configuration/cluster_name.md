# `cluster.name`

节点只有在与集群中所有其他节点的 `cluster.name` 保持一致时才能加入集群。默认是 `elasticsearch`，但您应该将其更改为描述集群用途的适当名称。
```
cluster.name: loggin-prod
```

请确保在不同的环境中集群名称不重复，否则可能会导致节点加入错误。

