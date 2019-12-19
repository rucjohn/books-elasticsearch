# Docker 安装方式

Elasticsearch 也可以用 Docker 镜像方式启动。镜像基于 [centos:7](https://hub.docker.com/_/centos/) 创建的。

在 [docker elastic](https://www.docker.elastic.co)上有公用 Docker 镜像列表和标签。源文件在 [Github](https://github.com/elastic/elasticsearch/tree/7.5/distribution/docker)。

这些软件包是在 Elastic License 下免费使用的。它包含开源代码和免费的商业功能，以及对付费商业功能的访问。可以试用30天，尝试所有的付费商业功能。有关 Elastic License 级别的信息，请参阅 [订阅](https://www.elastic.co/cn/subscriptions) 页

## 拉取镜像
```
docker pull docker.elastic.co/elasticsearch/elasticsearch:7.5.0
```

## 启动单节点集群
```
docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:7.5.0
```

## 使用 docker compose 启动多节点集群

配置三个节点 Elasticsearch 集群，使用 docker compose：

### 1. 创建 `docker-compose.yml` 文件
```
version: '2.2'
services:
  es01:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.5.0
    container_name: es01
    environment:
      - node.name=es01
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es02,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data01: /usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - elastic
  es02:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.5.0
    container_name: es02
    environment:
      - node.name=es02
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data02:/usr/share/elasticsearch/data
    networks:
      - elastic
  es03:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.5.0
    container_name: es03
    environment:
      - node.name=es03
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es02
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data03:/usr/share/elasticsearch/data
    networks:
      - elastic

volumes:
  data01:
    driver: local
  data02:
    driver: local
  data03:
    driver: local

networks:
  elastic:
    dirver: bridge
```

此示例 docker compose 文件将配置一个三节点的 Elasticsearch 集群。节点 es01 在本地监听 9200 端口，es02 和 es03 通过 Docker 网络与 es01 连接。

Docker 数据卷 data01、data02、data03 是 Docker 存储节点数据目录，以便数据在重启时操持不变。如果它们不存在，则在生成集群时将它们组合起来。

### 2. 分配内存

确保 Docker 引擎至少分配了 4G 内存。

### 3. 启动
```
docker-compose up
```

### 4. 验证
```
curl -XGET "localhost:9200/_cat/nodes?v&pretty"
```

日志消息进入控制台，由配置的 Docker 日志驱动程序处理。默认情况下，可以使用 docker 日志

运行 `docker-compose down` 停止集群。运行 `docker-compose up` 重启集群，将保留并加载 Docker 卷中的数据。若要删除数据卷，指定 -v 选项: `docker-compose down -v`。


## 生产环境使用 Docker 镜像

### 1. 设置 `vm.max_map_count`

- Linux
在 `/etc/sysctl.conf` 中配置 `vm.max_map_count`
```
grep "vm.max_map_count" /etc/sysctl.conf
vm.max_map_count=262144
```
执行生效
```
sysctl -w vm.max_map_count=262144
```

- macOS

- Windows


### 2. `elasticsearch` 用户必须能够读取配置文件

默认情况下，Elasticsearch 用 `elasticsearch` 用户在容器内运行，使用 uid:gid `1000:1000`。

> 重要：`Openshift` 是个特例，它使用任意分配的用户ID运行容器。`Openshift` 显示的是 gid 设置为 0 的持久卷，该操作无需任何调整。

如果挂载本地目录或文件，`elasticsearch` 用户必须能够读取该目录或文件。此外，用户必须具有对数据和日志目录有写访问权限。最好的策略是对本地目录授予 gid 1000 或 0 的访问权限。

例如，如果要准备挂载用于存储数据的本地目录：
```
mkdir esdatadir
chmod g+rwx esdatadir
chgrp 1000 esdatadir
```

最后，您可以通过环境变量 `TAKE_FILE_OWNERSHIP` 强制容器更新数据和日志目录时有挂载目录的权限。uid:gid `1000:0` 提供对 Elasticsearch 进程所需的读写访问。

### 3. 增加 nofile 和 nproc 限制

必须为 Elasticsearch 容器提供增加的 nofile 和 nproc 的 ulimit。验证 Docker 守护进程的 init 系统是否将它们设置为可接受的值。

要检查 Docker 守护进程的 ulimit 值：
```
docker run --rm centos:7 /bin/bash -c 'ulimit -Hn && ulimit -Sn && ulimit -Hu && ulimit -Su'
```

如果需要，可以在守护进程中调整，或者在每个容器中重写它们。例如，运行 `docker run` 时设置：
```
--ulimit nofile=65535:65535
```

### 4. 禁用 swap

为了提高性能和节点稳定性，需要禁用 swap 分区。

如果选择 `bootstrap.memory_lock: true`，还需要在 Docker 守护进程中定义 `memlock: true` 限制，或者显式地为容器设置。运行 `docker run` 时指定：
```
-e "bootstrap.memory_lock=true" --ulimit memlock=-1:-1
```

### 5. 分配对外端口

镜像对外 TCP 端口 9200 和 9300。对于生产集群，建议使用 `--publicsh-all` 随机公布端口，除非您为每个主机固定一个容器。

### 6. 设置内存

使用 `ES_JAVA_OPTS` 环境变量去设置内存大小。例如，要使用 16GB，请在 `docker run`中指定 `-e ES_JAVA_OPTS="-Xms16g -Xmx16g"`。

> 重要：即使限制对容器的内存资源使用，也必须配置内存大小。

### 7. 固定镜像版本

固定镜像版本。例如，`docker.elastic.co/elasticsearch/elasticsearch:7.5.0`

### 8. 始终挂载数据卷

您应该使用一个数据卷挂载在 `/usr/share/elasticsearch/data`，原因如下：
- 如果容器被 kill，elasticsearch 节点的数据不会丢失
- Elasticsearch 对 I/O 敏感，Docker 存储程序不适合快速 I/O
- 允许使用高级 Docker 卷插件

### 9. 避免使用 `loop-lvm` 模式

如果使用的是 devicemapper 存储驱动程序，请不要使用默认的 `loop-lvm` 模式。

### 10. 集中日志

考虑使用不同的日志驱动程序集中日志。还需注意，默认的 json 文件日志驱动程序并不适合生产使用。

### 11. 配置

运行 Docker是，从 `/usr/share/elasticsearch/config` 加载配置文件。

若要使用自定义配置文件，在镜像中将配置文件挂载到文件上。

可以使用 Docker 环境变量设置各个 Elasticsearch 配置参数。

还可以覆盖镜像的默认命令，以将 Elasticsearch 配置参数作为命令行选项传递。例如：
```
docker run <various parameters> bin/elasticsearch -Ecluster.name=mynewclustername
```

虽然挂载配置文件通常是生产中的首选方法，但也可以创建包含配置的自定义 Docker 镜像。

### 12. 挂载配置文件

创建自定义配置文件并将它们挂载到 Docker 镜像中相应的文件上。例如，使用 `docker run` 挂载 `custom_elasticsearch.yml`，指定：
```
-v full_path_to/custom_elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml
```

> 重要：容器使用 `elasticsearch`（uid:gid 1000:1000）用户运行。挂载的宿主目录和文件必须允许此用户访问，并且数据和日志目录必须允许此用户写入。

### 13. 自定义镜像

在某些环境中，准备包含配置的自定义镜像可能更有意义。实现此目的的 Dockerfile 可以简单做到：
```
FROM docker.elastic.co/elasticsearch/elasticsearch:7.5.1
COPY --chown=elasticsearch:elasticsearch elasticsearch.yml /usr/share/elasticsearch/config/
```

然后使用以下命令生成并运行镜像：
```
docker build --tag=elasticsearch-custom
docker run -it -v /usr/share/elasticsearch/data elasticsearch-custom
```

有些插件需要额外的安全权限。必须通过以下方式显式接受它们：
- 运行 Docker 镜像时附加 `tty`，并在提示时允许权限。
- 通过在 plugin install 命令中添加 `--batch` 标志来检查安全权限并接受它们（如果合适）。

