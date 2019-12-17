# Linux 或 macOS 安装方式

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


