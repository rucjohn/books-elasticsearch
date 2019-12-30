# 集群发现和格式设置

在投入生产之前，应该配置集群发现和格式设置这两个重要的配置，以便集群中的节点可以相互发现并选择主节点。

## `discovery.seed_hosts`

开箱即用，不需要任何网络配置，Elasticsearch 将绑定在可用的回环地址，并扫描本地端口 9300-9305，尝试连接到同一服务器上运行的其他节点。这提供了自动集群的体验，而无需进行任何配置。

如果要与其他主机上的节点组成集群，则必须使用 `discovery.seed_hosts` 设置来提供集群中其他节点的列表，这些节点是符合主节点资格的，并且可能是活动和可连接的，以便为 [发现进程](https://rucjohn.gitbook.io/elasticsearch/5-Aggregations/modules/discovery_and_cluster_formation/discovery) 广播。此设置通常应包含集群中所有符合主节点条件的节点地址。此设置包含主机数组或逗号分隔的字符串。每个值的格式是 `host:port` 或 `host`（其中 `port` 默认是 `transport.profiles.default.port`，如果没有设置，则返回 `transport.port`）。请注意，IPV6主机必须用括号括起来。此设置默认值是 `127.0.0.1`, `[::1]`。

## `cluster.initial_master_nodes`

当您第一次启动一个全新的 Elasticsearch 集群时，会有一个集群引导步骤，该步骤确定在第一次选举中投票成功的主节点。在开发模式下，在未配置发现设置的情况下，此步骤由节点本身自动执行。由于此自动引导步骤不安全，因此当您在生产模式下启动一下全新的集群时，必须显式列出在第一次选举中可投票的符合主节点资格的节点。此列表是使用 `cluster.initial_master_nodes` 此设置来设置的。当重启集群或向现有集群添加新节点时，不使用此设置。
```
discovery.seed_hosts:
  - 192.168.1.10:9300
  - 192.168.1.11				&spades;
  - seeds.mydomain.com 			&hearts;
cluster.initial_master_nodes:	&clubs;
  - master-node-a
  - master-node-b
  - master-node-c
```

> &spades; 端口默认是 `transport.profiles.default.port`，如果未指定，则返回 `transport.port`。

> &hearts; 如果域名解析为多个IP地址，则节点将尝试在所有解析的地址上发现其他节点。

> &clubs;  初始主节点应该由 `node.name` 标识，该名称默认为其主机名。确保 `cluster.initial_master_nodes` 中的值与 `node.name`  完全一致。如果使用完全限定的域名（如 `master-node-a.example.com`）作为节点名，则必须在此列表中使用完全限定的名称；反之，如果 `node.name` 是没有任何尾随限定符的裸主机名，则还必须省略 `cluster.initial_master_nodes` 的尾随限定符。

