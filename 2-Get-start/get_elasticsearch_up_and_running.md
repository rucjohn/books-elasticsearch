# 安装与运行

在Linux、macOS 或 Windows 计算机上配置多节点 Elasticsearch 集群。

## 在 Elastic Cloud 上运行 Elasticsearch

## 在 Linux、macOS 或 Windows 运行本地 Elasticsearch

通过 `tar` 或 `zip` 压缩包，可以在本地启动 Elasticsearch 多实例，查看多节点集群情况。

在本地运行三节点 Elasticsearch 集群：

1. 下载

- Linux: [elasticsearch-7.5.0-linux-x86_64.tar.gz](https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.5.0-linux-x86_64.tar.gz)
```
curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.5.0-linux-x86_64.tar.gz
```
- macOS: [elasticsearch-7.5.0-darwin-x86_64.tar.gz](https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.5.0-darwin-x86_64.tar.gz)
```
curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.5.0-darwin-x86_64.tar.gz
```
- Windows: [elasticsearch-7.5.0-windows-x86_64.zip](https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.5.0-windows-x86_64.zip)
```
powershell -Command "(New-Object Net.WebClient).DownloadFile('"https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.5.0-windows-x86_64.zip"', '"elasticsearch-7.5.0-windows-x86_64.zip"')"
```

2. 解压
- Linux
```
tar zxvf elasticsearch-7.5.0-linux-x86_64.tar.gz
```
- macOS
```
tar zxvf elasticsearch-7.5.0-darwin-x86_64.tar.gz
```
- Windows
```
Expand-Archive elasticsearch-7.5.0-windows-x86_64.zip
```

3. 启动
- Linux and macOS
```
cd elasticsearch-7.5.0/bin
./elasticsearch
```
- Windows
```
cd elasticsearch-7.5.0\bin
.\elasticsearch.bat
```
单节点 Elasticsearch 集群启动

4. 再启动两个 Elasticsearch 实例，这样就可以看到典型的多节点集群。需要为每个节点指定唯一的数据和日志路径。
- Linux and macOS
```
./elasticsearch -Epath.data=data2 -Epath.logs=log2
./elasticsearch -Epath.data=data3 -Epath.logs=log3
```
- Windows
```
.\elasticsearch.bat -E path.data=data2 -E path.logs=log2
.\elasticsearch.bat -E path.data=data3 -E path.logs=log3
```
它们会自动与集群中的第一个节点连接起来。

5. 使用 cat health API 验证三节点集群是否运行。cat APIs 以比原始JSON更容易展示的格式返回有关集群和索引的信息。

通过向 Elasticsearch REST API 提交 HTTP 请求，可以直接与集群交互。本指南中的大多数示例能够直接复制 `CURL` 命令并执行。如果您已经安装并运行了 Kibana，还可以打开 Kinaba 的 Dev 控制台提交请求。
```
GET /_cat/health?v
```
返回指标 Elasticsearch 集群的状态为绿色，有三个节点
```
epoch      timestamp cluster       status node.total node.data shards pri relo init unassign pending_tasks max_task_wait_time active_shards_percent
1565052807 00:53:27  elasticsearch green           3         3      6   3    0    0        0             0                  -                100.0%
```

> 如果只有单实例 Elasticsearch，集群状态为黄色。单个节点集群也能完全正常工作，但无法将数据复制到另一个节点。副本分片必须可用，集群状态才能是绿色。如果集群状态是红色，则表示某些数据不可用。

## 其它安装选项

通过压缩包安装 Elasticsearch，您可以轻松地在本地安装和运行多个实例。要运行单个实例，可以在 Docker 容器中运行 Elasticsearch，在 Linux 上使用 DEB 或 RPM 软件包安装 Elasticsearch，在 macOS 上使用自制软件安装，或在 Windows 上使用 MSI 软件包安装程序。有关详细信息，请参见 [安装 Elasticsearch](https://rucjohn.gitbook.io/elasticsearch/2-Set-up/installing_elasticsearch)




