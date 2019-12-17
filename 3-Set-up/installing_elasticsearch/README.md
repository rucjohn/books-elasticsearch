# 安装 Elasticsearch

## Elasticsearch 组织

您可以在自己的硬件上运行 Elasticsearch，也可以在 Elastic Cloud 上使用托管的 Elasticsearch 服务。在 AWS 和 GCP 上都可以使用 Elasticsearch 服务。

## 安装 Elasticsearch

Elasticsearch 提供了以下的包格式：

格式 | description
--- | ---
tar.gz | `tar.gz` 压缩包可以在任何 Linux 发行版和 macOS 上安装。
zip | `zip` 压缩包在 Windows 上安装。
rpm | `rpm` 适合安装在 RedHat, CentOS, SLES, OpenSuSE 和其他基于 rpm 的系统上。RPMs 可以从 Elasticsearch 网站 或 RPM 仓库下载。
deb | `deb` 适合安装在 Debian, Ubuntu 和其他基于 Debian 的系统上。Debian 可以从 Elasticsearch 网站 或 Debian 仓库下载。
msi | `msi` 包适合安装在至少安装了 .NET 4.5 framework 的 Windows 64 位系统上。MSIs 可以从 Elasticsearch 网站下载。
docker | 使用 Docker 容器 运行 Elasticsearch 镜像。可以从 Elastic Docker 仓库上下载。
brew | 可以从 Elastic Homebrew tap 获取，使用 Homebrew 软件包管理器在 macOS 上安装 Elasticsearch。

## 配置管理工具

我们还提供以下配置管理工具来䃲进行大型环境部署：

配置管理工具 | github
--- | ---
Puppet | [puppet-elasticsearch](https://github.com/elastic/puppet-elasticsearch)
Chef | [cookbook-elasticsearch](https://github.com/elastic/cookbook-elasticsearch)
Ansible | [ansible-elasticsearch](https://github.com/elastic/ansible-elasticsearch)
