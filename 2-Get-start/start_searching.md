# 搜索

一旦 Elasticsearch 索引中有数据，就可以通过 `_search` 目标发送搜索请求。要访问全部搜索功能，指定索引名和请求URI， 可以使用 Elasticsearch 查询 DSL 在 Body 中指定搜索条件。

例如，以下请求检索按帐号排序的 bank 索引中的所有文档：
```
GET /bank/_search
{
	"query": {
		"match_all": {}
	},
	"sort": [
		{
			"account_number": "asc"
		}
	]
}
```

默认情况下，响应的 `hits` 部分包含与搜索条件匹配的前 10 个文档：
```
{
	"took": 63,
	"timed_out": false,
	"_shards": {
		"total": 5,
		"successful": 5,
		"skipped": 0,
		"failed": 0
	},
	"hits": {
		"total": {
			"value": 1000,
			"relation": "eq"
		},
		"max_score": null,
		"hits": [
			{
				"_index": "bank",
				"_type": "_doc",
				"_id": "0",
				"sort": [0],
				"_score": null,
				"_source": {
					{"account_number":0,"balance":16623,"firstname":"Bradshaw","lastname":"Mckenzie","age":29,"gender":"F","address":"244 Columbus Place","employer":"Euron","email":"bradshawmckenzie@euron.com","city":"Hobucken","state":"CO"}
				}
			},
			{
				"_index": "bank",
				"_type": "_doc",
				"_id": "1",
				"sort": [0],
				"_score": null,
				"_source": {
					{"account_number":1,"balance":39225,"firstname":"Amber","lastname":"Duke","age":32,"gender":"M","address":"880 Holmes Lane","employer":"Pyrami","email":"amberduke@pyrami.com","city":"Brogan","state":"IL"}
				}
			},
			......
		]
	}
}
```

响应提供了以下有关搜索请求的信息：
- `took` - Elasticsearch 运行查询所用的时间（毫秒）
- `timed_out` - 搜索请求是否超时
- `_shards` - 搜索了多少分片，并对成功、失败或跳过的分片进行统计
- `max_score` - 找到的最相关文档的分数
- `hits.total.value` - 找到多少个匹配的文档
- `hits.sort` - 文档的排序（不按相关性分数排序时）
- `hits._score` - 文档的相关性分数（使用 `match_all` 时不适合 ）

每个搜索请求包含：Elasticsearch 不在请求间维护任何状态信息。要浏览搜索结果，请在请求中指向 `from` 和 `size` 参数。

例如，以下请求的命中率为 10 到 19：
```
{
	"query": {
		"match_all": {}
	},
	"sort": [
		{
			"account_number": "asc"
		}
	],
	"from": 10,
	"size": 10
}
```

既然您已经了解了如何提交基本的搜索请求，就可以开始构造比 `match_all` 更有趣的查询。

在一个字段中搜索特定字符，可以使用 `match` 查询。例如，在 `address` 字段中查找包含有 `mill` 或 `lane` 的地址的客户。
```
GET /bank/_search
{
	"query": {
		"match": {
			"address": "mill lane"
		}
	}
}
```

如果要执行短语搜索而不是匹配单词，可以使用 `match_phrase` 代替 `match`。例如，匹配包含短语 `mill lane` 的地址。
```
GET /bank/_search
{
	"query": {
		"match_phrase": {
			"address": "mill lane"
		}
	}
}
```

要构造更复杂的查询，可以使用 `bool` 查询组合多个查询条件。您可以根据"必须匹配"、"应该匹配"或"必须不匹配"来指定条件。

例如，在 `bank` 索引中搜索客户岁数40岁，但不住在 `Idaho (ID)` 的帐户。
```
GET /bank/_search
{
	"query": {
		"bool": {
			"must": [
				{"match": {"age": "40"}}
			],
			"must_not": [
				{"match": {"state": "ID"}}
			]
		}
	}
}
```

在 `bool` 查询中每个 `must`、`should`、`must_not` 元素都称为查询子句。文档满足每个 `must` 或 `should` 子句中的标准的程序有助于文档的相关性得分。分数越高，文档就越符合您的搜索条件。默认情况下，Elasticsearch 返回按相关性得分排序的文档。

`must_not` 子句中的条件被视为筛选器。它影响文档是否包含在结果中，但不影响文档的评分方式。您还可以显示地指定什么任意过滤器，以包含或排除基于结构化数据的文档。

例如，使用范围筛选器将结果限制在余额为 20000 到 30000（含） 美元之间的帐户。
```
GET /bank/_search
{
	"query": {
		"bool": {
			"must": {
				"match_all": {}
			},
			"filter": {
				"range": {
					"balance": {
						"gte": 20000,
						"lte": 30000
					}
				}
			}
		}
	}
}
```
