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





































