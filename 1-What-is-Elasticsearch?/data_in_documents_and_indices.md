# 数据输入：文档和索引

Elasticsearch 是一个分布式文档存储。Elasticsearch 不按行数据形式存储信息，而是以已序列化的 JSON 文档的复杂数据结构进行存储。当集群中有多个 Elasticsearch 节点时，存储的文档分布在集群中，并且可从任一节点进行访问。

文档存储后，将在接近实时（1秒内）地完成索引和全文检索。Elasticsearch 使用倒排索引的数据结构，该结构支持快速全文检索。倒排索引会列出文档中出现的每个唯一单词，并标记每个单词出现的所有文档。

索引可以看作是文档的集合，每个文档都是一组字段的集合，字段是包含数据的 "key:value"。默认情况下，Elasticsearch 对每个字段中的所有数据进行索引，并且每个索引字段都有一个专用的优化数据结构。例如，文本字段存储在倒排索引中，数字和地理字段存储在 BKD 树中。使用每个字段的数据结构来组合和返回搜索结果的能力是 Elasticsearch 如此之快的原因。

Elasticsearch 还具有无模式的能力，这意味着可以为文档编制索引，而无需显式得指定如何处理文档中可能出现的每个不同字段。启用动态映射后，Elasticsearch 会自动检测并向索引中添加新字段。这种默认行为使得索引和搜索数据变得更容易 --- 只需要开始创建索引文档，Elasticsearch 就会检测布尔值、浮点数和整数值、日期和字符串，并将其映射到对应的 Elasticsearch 数据类型中。

但是，最后，你应该要比 Elasticsearch 更了解你的数据，才能知道如何去使用它。可以定义规则来控制动态映射，也可显式定义映射来完全控制字段的存储和索引方式。

自定义映射：
- 区分大小写的字符串字段和精确值的字符串字符
- 执行特定语言的文本分析
- 优化部分匹配字段
- 使用自定义日期格式
- 使用无法自动检测的数据类型，如`geo_point`和`geo_shape`

根据不同的目的，使用不同的方式对同一字段建立索引是很有用的。例如，可能希望将字符串字段索引为全文检索的 `text` 字段和用于排序或聚合的 `keyword` 字段。又或者，可以选择多个语言解析器来处理包含用户输入的字符串字段内容。

在索引期间，应用于全文本字段的分析链也可在查询时使用。当在查询全文本字段时，对查询文本进行相同的分析，然后再在索引中查找术语。