# 使用聚合分析结果

Elasticsearch 聚合使您能够获得有关搜索结果的元信息，并回答诸如『德克萨斯州有多少帐户持有人』或者『回纳西州的平均帐户余额是多少？』之类的问题。您可以在一个请求中搜索文档、筛选命中率，并且使用聚合分析结果。

例如，在`bank` 索引中使用 `terms` 聚合按状态对所有帐户进行分组。
```
GET /bank/_search
{
	"size": 0,
	"aggs": {
		"group_by_state": {
			"terms": {
				"field": "state.keyword"
			}
		}
	}
}
```

响应中的 `buckets` 是 `state` 的值。`doc_count` 显示每个状态的文档总数。例如，可以看到状态为 "ID" 的有27个帐户（文档数）。因为 `size=0`，所以响应中只包含了聚合结果。
```
{
	"took": 29,
	"timed_out": false,
	"_shards": {
		"total": 5,
		"successful": 5
		"skipped": 0,
		"failed": 0
	},
	"hits": {
		"total": {
			"value": 1000,
			"relation": "eq"
		},
		"max_score": null,
		"hits": []
	},
	"aggregations": {
		"group_by_state": {
			"doc_count_error_upper_bound": 20
			"sum_other_doc_count": 770,
			"buckets": [
				{
					"key": "ID",
					"doc_count": 27
				},
				{
					"key": "TX",
					"doc_count": 27
				},
				{
					"key": "AL",
					"doc_count": 25
				},
				......
			]
		}
	}
}
```

您可以组合聚合来构建更复杂的数据摘要。例如，将 `avg` 聚合嵌套在之前的按状态分组的聚合中，以计算每个状态的平均帐户余额。
```
GET /bank/_search
{
	"size": 0,
	"aggs": {
		"group_by_state": {
			"terms": {
				"field": "state.keyword"
			},
			"aggs": {
				"average_balance": {
					"avg": {
						"field": "balance"
					}
				}
			}
		}
	}
}
```

您可以通过在 `terms` 聚合中指定排序方式，使用嵌套聚合的结果进行排序，替代计数排序。
```
GET /bank/_search
{
	"size": 0,
	"aggs": {
		"group_by_state": {
			"terms": {
				"field": "state.keyword",
				"order": {
					"average_balance": "desc"
				}
			},
			"aggs": {
				"average_balance": {
					"avg": {
						"field": "balance"
					}
				}
			}
		}
	}
}
```
