# RPM安装方式

Elasticsearch RPM 可以从官网或 RPM 仓库下载。可以在任何基于 RPM 的系统上安装 Elasticsearch，如 OpenSuSE、SLES、CentOS、RedHat 和 Oracle Enterprise。

> 在旧版本的 Linux 发行版操作系统（如 SLES 和 CentOS 5）上不支持 RPM 安装。可以参阅上一章节 `压缩包安装方式`。

这些软件包是在 Elastic License 下免费使用的。它包含开源代码和免费的商业功能，以及对付费商业功能的访问。可以试用30天，尝试所有的付费商业功能。有关 Elastic License 级别的信息，请参阅 [订阅](https://www.elastic.co/cn/subscriptions) 页

最新稳定版本可以在 [Download Elasticsearch](https://www.elastic.co/cn/downloads/elasticsearch) 页面找到。其他版本可以在 [Past Releases](https://www.elastic.co/cn/downloads/past-releases) 页面找到。

## 导入 PGP 密钥

我们使用 Elasticsearch 自己的签名密钥（PGP密钥：[D88E42B4](https://pgp.mit.edu/pks/lookup?op=vindex&search=0xD27D666CD88E42B4)，可以从 `https://pgp.mit.edu` 获取）和指纹对所有包进行签名：
```
4609 5ACC 8548 582C 1A26 99A9 D27D 666C D88E 42B4
```

下载安装公钥
```
rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
```

## 基于仓库安装 RPM

- 在基于 RedHat 发行版操作系统的 `/etc/yum.repos.d/` 目录下创建 `elasticsearch.repo` 文件；
- 在基于 OpenSuSE 发行版操作系统的 `/etc/zypp/repos.d/` 目录下创建 `elasticsearch.repo` 文件；

```
[elasticsearch]
name=Elasticsearch repository for 7.x packages
baseurl=https://artifacts.elastic.co/packages/7.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=0
autorefresh=1
type=rpm-md
```

当你的仓库配置完成。可以使用以下其中一个命令来安装 Elasticsearch：
```
sudo yum install --enablerepo=elasticsearch elasticsearch
sudo dnf install --enablerepo=elasticsearch elasticsearch
sudo zypper modifyrepo --enable elasticsearch && \
	sudo zypper install elasticsearch; \
	sudo zypper modifyrepo --disable elasticsearch
```

- `yum`适用 CentOS 和 旧的基于 RedHat 分发版本，如（<= CentOS 7.x）
- `dnf`适用 Fedora 和 其他新的 RedHat 分发版本，如（>= CentOS 8.x）
- `zypper`适用基于 OpenSUSE 分发版本


> 默认情况下要禁用 elasticsearch 仓库，以消除在升级系统过程中意外升级 elasticsearch 的可能性。每个 install 或 upgrade 命令都必须显式地显示仓库名。

## 手动下载安装 RPM
```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.5.0-x86_64.rpm
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.5.0-x86_64.rpm.sha512
shasum -a 512 -c elasticsearch-7.5.0-x86_64.rpm.sha512
sudo rpm --install elasticsearch-7.5.0-x86_64.rpm
```

## SysV `init` 运行 Elasticsearch

使用 `chkconfig` 命令去配置 elasticsearch 服务自启动：
```
sudo chkconfig --add elasticsearch
```

使用 `service` 命令启动停止 Elasticsearch
```
sudo -i service elasticsearch start
sudo -i service elasticsearch stop
```

如果 Elasticsearch 由于其他原因启动失败，会打印失败原因到 STDOUT.log 文件中，文件存储在 `/var/log/elasticsearch/`目录下。

## `systemd` 运行 Elasticsearch

配置 elasticsearch 服务自启动：
```
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable elasticsearch.service
```

Elasticsearch 启动和停止：
```
sudo systemctl start elasticsearch.service
sudo systemctl stop elasticsearch.service
```

这些命令不提供有关 Elasticsearch 是否成功启动的反馈。这些信息会写入 `/var/log/elasticsearch/` 目录下的日志文件中。

默认情况下，Elasticsearch 服务不会在 systemd 日志中记录信息。要启用 `journalctl` 日志记录，必须从 `elasticsearch.service` 文件的 `ExecStart` 参数中删除 `--quiet` 选项。

启用 systemd 日志时，可以使用 `journalctl` 命令获取日志信息：
```
sudo journalctl -f
```

只显示 elasticsearch 服务的日志：
```
soduo journalctl --unit elasticsearch
```

按开始时间显示 elasticsearch 服务日志：
```
sudo journalctl --unit elasticsearch --since "2016-10-30 18:17:16"
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

## 配置 Elasticsearch

Elasticsearch 默认使用 `/etc/elasticsearch` 在运行时配置。此目录和目录下的文件的所有权都是 `root:elasticsearch` ，并且设置了 `setgid` 标志，以便 elasticsearch 程序可以通过组权限读取此目录下的文件。

Elasticsearch 通过 `/etc/elasticsearch/elasticsearch.yml` 文件加载配置。

RPM 中还有一个系统配置文件（`/etc/sysconfig/elasticsearch`），允许您设置以下参数：

参数 | 描述
--- | ---
JAVA_HOME | 自定义 Java 路径
MAX_OPEN_FILES | 句柄数，默认是65535
MAX_LOCKED_MEMORY | 最大锁定内存大小。如果使用 `elasticsearch.yml` 中的 `bootstrap.memory_lock` 选项，则设置为 `unlimited`
MAX_MAP_COUNT | 进程可能具有的最大内存映射区域数。如果使用 `mmapfs` 作为索引存储类型，请确保将其设置为高值。这是在启动 Elasticsearch 之前通过 `sysctl` 设置的。默认是 `262144`
ES_PATH_CONF | 配置文件目录（需要包括 `elasticsearch.yml`、`jvm.options`、`log4j2.properties`）；默认是 `/etc/elasticsearch`
ES_JAVA_OPTS | JVM 配置
RESTART_ON_UPGRADE | 在进行包更新的时候配置重启，默认是 `false`。这意味着您必须在手动安装包重启 Elasticsearch 实例。这样做的原因是为了确保集群中的升级不会导致连接的分片重新分配，从而导致高流量并降低集群的响应时间。


## RPM 目录布局

RPM 将配置文件、日志和数据目录旋转在以下目录：

Type | Description | Default Location | Setting
--- | --- | --- | ---
home | Elasticsearch 主目录，或 `$ES_HOME` | `/usr/share/elasticsearch` | 
bin | 二进制脚本，包括用于启动节点的 `elasticsearch` 和用于安装插件的 `elasticsearch-plugin` | `/usr/share/elasticsearch/bin`
conf | 配置文件，包含有 `elasticsearch.yml` | `/etc/elasticsearch`
conf | 包括堆大小、文件描述符等环境变量 | `/etc/sysconfig/elasticsearch`
data | 在节点上分配的每个索引 / 分片的数据文件的路径。可以有多个路径 | `/var/lib/elasticsearch` | path.data
jdk | JDK 路径，可以通过 `/etc/sysconfig/elasticsearch`中设置 `JAVA_HOME` 环境变量来覆盖 | `/usr/share/elasticsearch/jdk`
logs | 日志文件位置 | `/var/log/elasticsearch` | path.logs
plugins | 插件文件位置。每个插件都将包含在一个子目录中。 | `/usr/share/elasticsearch/plugins` |
repo | 共享文件系统存储库位置。可以有多个路径。文件系统存储库可以放在任意目录下。 |  | path.repo

