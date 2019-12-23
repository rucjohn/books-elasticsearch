# 配置 Elasticsearch

Elasticsearch 有良好的默认值，只需要很少的配置。可以使用更新设置 API 对正在运行的集群更改大多数的设置。

配置文件应包含特定于节点的设置（如 `node.name` 和路径等 ），或者节点加入集群的设置，如 `cluster.name` 和 `network.host`。

## 本地配置

Elasticsearch 有三个配置文件：
- `elasticsearch.yml`，配置 Elasticsearch
- `jvm.options`，配置 Elasticsearch JVM 设置
- `log4j2.properties`， 配置 Elasticsearch 日志

这些文件位于 `config` 目录中，其默认位置取决于安装是否来自压缩包还是RPM包。

对于压缩包，配置文件目录默认是 `$ES_HOME/config`。配置目录的位置可以通过 `ES_PATH_CONF` 环境变量更改：
```
ES_PATH_CONF=/path/to/my/config ./bin/elasticsearch
```

或者，可以通过命令行或 shell 配置 `ES_PATH_CONF` 环境变量。

至于 RPM 包或 Debian 包，配置目录位置默认是 `/etc/elasticsearch`。配置目录也可以通过 `ES_PATH_CONF` 环境变量进行更改。但需要注意，在 shell 中设置是不够的，这个变量来自 `/etc/default/elasticsearch`(Debian) 和 `/etc/sysconfig/elaticsearch`(RPM)。您需要相应地编辑其中一个文件中的 `ES_PATH_CONF=/etc/elasticsearch` 记录以更改配置目录位置。

## 配置文件格式

默认配置格式是 `YAML`。以下是数据目录和日志目录的配置示例：
```
path:
  data: /var/lib/elasticsearch
  logs: /var/log/elasticsearch
```

也可以以下配置：
```
path.data: /var/lib/elasticsearch
path.logs: /var/log/elasticsearch
```

## 环境变量替换

配置文件中使用 `${...}` 符号引用的环境变量将替换为环境变量的值，例如：
```
node.name: ${HOSTNAME}
network.host: ${ES_NETWORK_HOST}
```
