
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
