# JVM 设置

您应该很少需要更改 JVM 选项。如果需要，最大可能就是设置内存大小。本文档的其余部分将详细说明如何设置 JVM 选项。

设置 JVM 选项（包括系统属性和 JVM 标志）的首先方法是通过 `JVM.options` 配置文件。此文件的的默认路径是 `config/jvm.options`（压缩包安装方式）和 `/etc/elasticsearch/jvm.options`（包安装方式）。

JVM 参数列表，遵循以下特殊语法：
- 空格的行将被忽略
- 以 `#` 开头的行被视为注释，并被忽略
- 以 `-` 开头的行被视为独立于 JVM 版本（通用）的 JVM 选项
```
-Xmx2g
```
- 以数字开头，后跟 `:` 和 `-`的行，仅当 JVM 版本与该数字匹配时才适用
```
8:-Xmx2g
```
- 以数字开头，后跟 `-` 和 `:`的行，仅当 JVM 的版本大于或等于该数字时才适用
```
8-:-Xmx2g
```
- 以数字开头，后跟 `-` 再跟数字，然后跟 `:`的行，仅当JVM 的版本在这两个数字的范围内时才适用
```
8-9:-Xmx2g
```
- 所有其他的行都拒绝


你可以自定义 JVM 标志添加到文件中，将依将配置检查到您的版本控制系统中。

设置 JVM 选项的另一种方法是通过 `ES_JAVA_OPTS` 环境变量。例如：
```
export ES_JAVA_OPTS="$ES_JAVA_OPTS -Djava.io.tmpdir=/path/to/temp/dir"
./bin/elasticsearch
```

JVM 有一个用于观察 `JAVA_TOOL_OPTIONS` 环境变量的内置机制。我们故意在打包脚本中忽略此环境变量。其主要原因是，在某些操作系统（如 Ubuntu）上，默认情况下会通过此环境变量安装一些代理，我们不希望干扰到 Elasticsearch。

此外，其他一些 Java 程序也支持 `JAVA_OPTS` 环境变量。这不是 JVM 中内置的机制，而是生态系统中的约定。但是，我们不支持此环境变量，而是支持通过 `JVM.options` 文件或 `ES_JAVA_OPTS` 环境变量设置 JVM 选项。
