# 索引生命周期管理设置

以下是可用于配置索引生命周期管理的设置

## 集群级别设置

### xpack.ilm.enabled

无法 ILM 是启动还是禁用，将其设置为 `false` 都会禁用任何 ILM REST API 端口和功能。默认是 `true`。

### indices.lifecycle.poll_interval

索引生命周期管理检查符合策略条件的索引的频率。默认值为 1000 万。

## 索引级别设置

这些索引级别的 ILM 设置通常通过索引模板进行配置。有关详细信息，请参阅 [Setting up a policy]()。

### index.lifecycle.name

用于管理索引的策略的名称。

### index.lifecycle.rollover_alias

索引滚动更新时的索引别名。指定何时使用包含滚动操作的策略。当索引滚动时，别名将更新，以反映该索引不再是写索引。有关滚动更新的详细信息，请参阅 [Using policies to manage index rollover]()。

### index.lifecycle.parse_origination_date

当配置为 `true` 时，将从索引名中分析起始日期。索引格式必须与模式 `^.*-{date_format}-\\d+`匹配，其中 `date_format` 是 `yyyy.MM.dd`，尾随数字是可选的（被滚动的索引通常与完整格式匹配，例如 `logs-2016.10.31-000002`）。如果索引名与模式不匹配，则索引创建将失败。

### index.lifecycle.origination_date

将用于计算其相恋的索引期限的时间戳。这允许用户创建包含旧数据的索引，并使用旧数据的原始创建日期来计算索引期限。
