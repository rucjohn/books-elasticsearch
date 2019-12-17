
在 Linux 或 macOS 上使用 `.tar.gz` 压缩包安装 Elasticsearch。

这些软件包是在 Elastic License 下免费使用的。它包含开源代码和免费的商业功能，以及对付费商业功能的访问。可以试用30天，尝试所有的付费商业功能。有关 Elastic License 级别的信息，请参阅 [订阅](https://www.elastic.co/cn/subscriptions) 页

最新稳定版本可以在 [Download Elasticsearch](https://www.elastic.co/cn/downloads/elasticsearch) 页面找到。其他版本可以在 [Past Releases](https://www.elastic.co/cn/downloads/past-releases) 页面找到。

## 下载并安装 Java

安装 Elasticsearch 之前，你需要先安装一个较新的版本的 Java，最好的选择是，你可以从 [www.java.com](https://www.java.com/zh_CN/download/) 获得官方提供的最新版本的 Java。

```
tar -zxf jdk-8u231-linux-x64.tar.gz
mv jdk-8u231-linux-x64 /usr/local/jdk
echo 'export JAVA_HOME=/usr/local/jdk' >> /etc/profile
echo 'export JRE_HOME=$JAVA_HOME/jre' >> /etc/profile
echo 'export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib' >> /etc/profile
echo 'export PATH=$JAVA_HOME/bin:$PATH' >> /etc/profile
source /etc/profile
```


## 下载 Linux 压缩包
```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.5.0-linux-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.5.0-linux-x86_64.tar.gz.sha512
shasum -a 512 -c elasticsearch-7.5.0-linux-x86_64.tar.gz.sha512
tar -xzf elasticsearch-7.5.0-linux-x86_64.tar.gz
cd elasticsearch-7.5.0/
```

## 下载 macOS 压缩包
```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.5.0-darwin-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.5.0-darwin-x86_64.tar.gz.sha512
shasum -a 512 -c elasticsearch-7.5.0-darwin-x86_64.tar.gz.sha512 
tar -xzf elasticsearch-7.5.0-darwin-x86_64.tar.gz
cd elasticsearch-7.5.0/ 
```

## 命令行启动 Elasticsearch
```
./bin/elasticsearch
```
默认情况下，Elasticsearch 在前台运行，将其日志打印到标准输出（stdout），并可以通过 `Ctrl+C` 停止。

## 服务方式启动 Elasticsearch

使用服务方式启动 Elasticsearch，目前只支持 CentOS/RedHat 7 以上版本。

创建用于启动 elasticsearch 程序的用户
```
useradd -M -s /sbin/nologin elasticsearch
```
> 尽量不要使用 `root` 用户启动程序，可以自定义的用户，如 `elasticsearch` 用户，也可以使用系统自带的 `nobody` 用户。
> 如何使用 `nobody` 用户，以下示例中涉及用户、分组和权限的部分，则需要相对应进行更改。

将 elasticsearch 主目录拷贝至 `/usr/local`，并配置权限
```
mv elasticsearch-7.5.0 /usr/local/elasticsearch
chown -R elasticsearch.elasticsearch /usr/local/elasticsearch
```

创建服务文件 `/usr/lib/systemd/system/elasticsearch.service`
```
[Unit]
Description=Elasticsearch 7.5.0
Documentation=http://www.elastic.co
Wants=network-online.target
After=etwork-online.target

[Service]
RuntimeDirectory=elasticsearch
PrivateTmp=true
Environment=JAVA_HOME=/usr/local/jdk
Environment=ES_HOME=/usr/local/elasticsearch
Environment=ES_PATH_CONF=/usr/local/elasticsearch/config
Environment=PID_DIR=/tmp
WorkingDirectory=/usr/local/elasticsearch
User=elasticsearch
Group=elasticsearch
ExecStart=/usr/local/elasticsearch/bin/elasticsearch -p ${PID_DIR}/elasticsearch.pid --quiet
StandardOutput=journal
StandardError=inherit
LimitNOFILE=655350
LimitNPROC=4096
LimitAS=infinity
LimitFSIZE=infinity
LimitMEMLOCK=infinity
TimeoutStopSec=0
KillSignal=SIGTERM
KillMode=process
SendSIGKILL=no
SuccessExitStatus=143

[Install]
WantedBy=multi-user.target
```

加载服务
```
systemctl daemon-reload
```

启动服务
```
systemctl start elasticsearch
```

使用以下命令查看服务启动情况
```
systemctl status elasticsearch
```

## 检查 Elasticsearch 运行情况

可以通过 `localhost` 的 `9200` 端口发送 HTTP 请求来测试您的 Elasticsearch 节点是否正在运行：
```
GET /
```

应该会有如下响应：
```
{
  "name" : "Cp8oag6",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "AT69_T_DTp-1qgIJlatQqA",
  "version" : {
    "number" : "7.5.0",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "f27399d",
    "build_date" : "2016-03-30T09:51:41.449Z",
    "build_snapshot" : false,
    "lucene_version" : "8.3.0",
    "minimum_wire_compatibility_version" : "1.2.3",
    "minimum_index_compatibility_version" : "1.2.3"
  },
  "tagline" : "You Know, for Search"
}
```

可以使用命令行上的 `-q` 或 `--quiet` 选项禁止输出`stdout`

## 启动一个daemon

要将 Elasticsearch 作为守护进程运行，请在命令行中指定 `-d`，并使用 `-p` 选项将进程ID记录在文件中
```
./bin/elasticsearch -d -p pid
```
日志消息会存储在 `$ES_HOME/logs/` 目录下

要关闭 Elasticsearch，`kill` 记录在 `pid` 文件中的进程ID：
```
pkill -F pid
```

## 命令行配置 Elasticsearch

默认情况下，Elasticsearch 从 `$ES_HOME/config/elasticsearch.yml` 文件中加载配置。

配置文件中的设置同样也可以在命令行中指定，使用 `-E` 选项：
```
./bin/elasticsearch -d -Ecluster.name=my_cluster -Enode.name=node_1
```

> 注意：通常，任何集群相关的设置（如 `cluster.name`）应该添加到 `elasticsearch.yml` 文件中，节点相关的设置（如 `node.name`）可以命令行上指定。

## 目录布局

分发版本的压缩包是完全独立的。默认情况下，所有文件和目录都应包含在 `$ES_HOME` --- 解压缩包时创建的目录中。

这非常方便，因为您不必创建任何其他目录就可以使用 Elasticsearch，卸载 Elasticsearch 就和删除目录一样简单。建议更改配置目录、数据目录和日志目录的默认位置，以便以后不会删除重要数据。

Type | Description | Default Location | Setting
--- | --- | --- | ---
home | Elasticsearch 主目录，或 `$ES_HOME` | 解压时创建 | 
bin | 二进制脚本，包括用于启动节点的 `elasticsearch` 和用于安装插件的 `elasticsearch-plugin` | `$ES_HOME/bin`
conf | 配置文件，包含有 `elasticsearch.yml` | `$ES_HOME/config`
data | 在节点上分配的每个索引 / 分片的数据文件的路径。可以有多个路径 | `$ES_HOME/data` | path.data
logs | 日志文件位置 | `$ES_HOME/logs` | path.logs
plugins | 插件文件位置。每个插件都将包含在一个子目录中。 | `$ES_HOME/plugins` |
repo | 共享文件系统存储库位置。可以有多个路径。文件系统存储库可以放在任意目录下。 |  | path.repo

