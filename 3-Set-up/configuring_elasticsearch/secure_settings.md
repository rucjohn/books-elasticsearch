# Secure 设置

有些设置是敏感的，单依赖文件系统权限保护是不够的。针对这个问题，Elasticsearch 提供了一个 keystore 和 `elasticsearch-keystore` 工具来管理 keystore 中的设置。

> 注意：这里所有的命令都应该在 Elasticsearch 的用户下运行。

> 重要：只有一些设置是被设计成从 keystore 中读取。但是，keystore 没有时间限制来阻止不支持的设置。将不支持的设置添加到 keystore 将导致 Elasticsearch 无法启动。请参阅每个设置的文档，以查看是否支持将其作为 keystore 的一部分。

> 注意：对 keystore 的所有修改仅在重启 Elasticsearch 后生效。

> 注意：Elasticsearch keystore 目前只提供模糊处理。以后会增加密码保护。

这些设置，就像 `elasticsearch.yml` 配置文件中的常规配置一样，需要在集群中的每个节点上指定。目前，所有安全设置都是特定于节点的设置，必须在每个节点上具有相同的值。

## 创建 keystore

创建 `elasticsearch.keystore` 文件，需要用到 `create` 命令：
```
bin/elasticsearch-keystore create
```

文件 `elasticsearch.keystore` 将与 `elasticsearch.yml` 一同创建。

## 在 keystore 中列出设置

使用 `list` 命令获得 keystore 中有效的设置列表：
```
bin/elasticsearch-keystore list
```

## 增加字符串设置

可以使用 `add` 命令添加敏感字符串设置，如云插件的身份验证凭据：
```
bin/elasticsearch-keystore add the.setting.name.to.set
```

提示输入需设置的值。也可以通过 stdin 传参，使用 `stdin` 标识：
```
cat /file/containing/setting/value | bin/elasticsearch-keystore add --stdin the.setting.name.to.set
```

## 增加文件设置

可以使用 `add-file` 命令添加敏感文件，如云插件的身份验证密钥文件。请在设置名之后，填写正确的文件路径。
```
bin/elasticsearch-keystore add-file the.setting.name.to.set /path/example-file.json
``` 

## 移除设置

使用 `remove` 命令从 keystore 中移除设置：
```
bin/elasticsearch-keystore remove the.setting.name.to.remove
```

## 升级 keystore

有时，keystore 的内部格式会更改。当从包管理器安装 Elasticsearch时，在包升级期间将磁盘上的 keystore 自动升级到新格式。在其他情况下，Elasticsearch 将在节点启动期间执行此类升级。这要求 Elasticsearch 对包含 keystore 的目录具有写权限。或者，也要以使用 `upgrade` 命令手动执行此类升级：
```
bin/elasticsearch-keystore upgrade
```

## 可重载的安全设置

与 `elasticsearch.yml` 的设置一样，对 keystore 内容的更改不会自动应用于正在运行的 Elasticsearch 节点。重新读取设置需要重启节点。但是，某些安全设置被标记为可重载。这些设置可以在被重新读取并应用于正在运行的节点。

所有可重载或不可重载的安全设置，在所有集群节点上都必须相同。在进行所需的安全设置更改之后，使用 `bin/elasticsearch-keystore add` 命令调用。

```
POST _nodes/reload_secure_settings
```
此 API 将解密并重新读取每个集群节点上的整个 keystore，但只应用可重载的安全设置。对其他设置的更改在下次重启之前不生效。调用返回后，重新加载已完成，这意味着依赖于这些设置的所有内部数据结构都已更改。一切都应用看起来好像设置是从一开始就有了的一样。

更改多个可重载的安全设置时，请在每个集群节点上完成修改所有设置，然后请求 `reload_secure_settings`，而不是每次修改都重新加载。





























