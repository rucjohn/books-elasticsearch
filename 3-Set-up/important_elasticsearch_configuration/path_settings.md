# `path.data` & `path.logs`

如果您使用 `.zip` 或 `.tar.gz` 压缩包，则数据和日志目录是 `$ES_HOME` 的子目录。如果这些重要文件夹保留在其默认位置，则在 Elasticsearch 升级到新版本时，这些目录可能被删除。

生产环境中，数据和日志目录路径几乎肯定要更改：
```
path:
  logs: /var/log/elasticsearch
  data: /var/data/elasticsearch
```

RPM 和 Debian 发行版已经为数据和日志使用了自定义路径。

`path.data` 可以设置多个路径，在这种情况下，所有路径都将用于存储数据（单个分片的所有文件都将存存储在同一数据路径上）：
```
path:
  data:
    - /mnt/elasticsearch_1
    - /mnt/elasticsearch_2
    - /mnt/elasticsearch_3
```
