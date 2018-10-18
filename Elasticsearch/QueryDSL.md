# Query
## 查询所有文档 match_all
```
GET /bank/_search
{
  "query": { "match_all": {} }
}
```
## 设置返回的结果大小 szie
通过设置 size
```
GET /bank/_search
{
  "query": { "match_all": {} },
  "size": 1
}
```
## 返回指定下标开始的文档 from
通过设置`from` 如下会返回 10 - 19
```
GET /bank/_search
{
  "query": { "match_all": {} },
  "from": 10,
  "size": 10
}
```

## 排序 sort
```
GET /bank/_search
{
  "query": { "match_all": {} },
  "sort": { "balance": { "order": "desc" } }
}
```

## 只返回指定字段 _source
返回_source只包含"account_number", "balance"
```
GET /bank/_search
{
  "query": { "match_all": {} },
  "_source": ["account_number", "balance"]
}
```
如果文档没有该字段，那么返回的结果也没有该字段。

## 匹配查询 match
查询account_number=20的文档
```
GET /bank/_search
{
  "query": { "match": { "account_number": 20 } }
}
```  
查询地址包含"mill"的文档(mill为一个单词)
```
GET /bank/_search
{
  "query": { "match": { "address": "mill" } }
}
```
查询地址包含"mill"或"lane"的文档
```
GET /bank/_search
{
  "query": { "match": { "address": "mill lane" } }
}
```

## bool 查询
### 查询地址同时包含"mill"和"lane"的文档 must
```
{
	"query":{
		"bool":{
			"must":[
				{"match":{"address":"mill"}},
				{"match":{"address":"lane"}}
			]
		}
	}
}
````
bool must 字句指定条件必须全部满足文档才会返回（即&&)

### 返回地址包含"mill"或"lane"的文档 should
```
{
	"query":{
		"bool":{
			"should":[
				{"match":{"address":"mill"}},
				{"match":{"address":"lane"}}
			]
		}
	}
}
```
should 即满足其中一个条件即可(即||)

### 地址即不包含"mill"，也不包含"lane"的文档 must_not
```
GET /bank/_search
{
  "query": {
    "bool": {
      "must_not": [
        { "match": { "address": "mill" } },
        { "match": { "address": "lane" } }
      ]
    }
  }
}
```
must_not 内部条件都为flase时，才满足返回条件
### 返回age为40并且state不为ID的文档
```
{
  "query": {
    "bool": {
      "must": [
        { "match": { "age": "40" } }
      ],
      "must_not": [
        { "match": { "state": "ID" } }
      ]
    }
  }
}
```


# Filters
## range
返回余额20000到30000的文档
```
{
	"query":{
		"bool":{
			"must":{
				"match_all":{}
			},
			"filter":{
				"range":{
					"balance":{
						"gte":20000,
						"lte":30000
					}
				}
			}
		}
	}
}
```

# 聚合 Aggregations
## 按照 state 分组 terms
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
返回的结果
```
{
  "took": 90,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 1000,
    "max_score": 1,
    "hits": [
      {
        "_index": "bank",
        "_type": "_doc",
        "_id": "25",
        "_score": 1,
        "_source": {
          "account_number": 25,
          "balance": 40540,
          "firstname": "Virginia",
          "lastname": "Ayala",
          "age": 39,
          "gender": "F",
          "address": "171 Putnam Avenue",
          "employer": "Filodyne",
          "email": "virginiaayala@filodyne.com",
          "city": "Nicholson",
          "state": "PA"
        }
      }
    ]
  },
  "aggregations": {
    "group_by_state": {
      "doc_count_error_upper_bound": 20,
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
        {
          "key": "MD",
          "doc_count": 25
        },
        {
          "key": "TN",
          "doc_count": 23
        },
        {
          "key": "MA",
          "doc_count": 21
        },
        {
          "key": "NC",
          "doc_count": 21
        },
        {
          "key": "ND",
          "doc_count": 21
        },
        {
          "key": "ME",
          "doc_count": 20
        },
        {
          "key": "MO",
          "doc_count": 20
        }
      ]
    }
  }
}
```
和下面的SQL是类似的
`SELECT state, COUNT(*) FROM bank GROUP BY state ORDER BY COUNT(*) DESC LIMIT 10;`
size=0 是我们不显示hits,因为我们只要聚合结果。

## 查询每个州的平均存款 avg
```
{
	"size":0,
	"aggs":{
		"group_by_state":{
			"terms":{
				"field":"state.keyword"
			},
			"aggs":{
				"average_balance":{
					"avg":{
						"field":"balance"
					}
				}
			}
		}
	}
}
```
结果
```
{
    "took": 50,
    "timed_out": false,
    "_shards": {
        "total": 5,
        "successful": 5,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": 1000,
        "max_score": 0,
        "hits": []
    },
    "aggregations": {
        "group_by_state": {
            "doc_count_error_upper_bound": 20,
            "sum_other_doc_count": 770,
            "buckets": [
                {
                    "key": "ID",
                    "doc_count": 27,
                    "average_balance": {
                        "value": 24368.777777777777
                    }
                },
                {
                    "key": "TX",
                    "doc_count": 27,
                    "average_balance": {
                        "value": 27462.925925925927
                    }
                },
                {
                    "key": "AL",
                    "doc_count": 25,
                    "average_balance": {
                        "value": 25739.56
                    }
                },
                {
                    "key": "MD",
                    "doc_count": 25,
                    "average_balance": {
                        "value": 24963.52
                    }
                },
                {
                    "key": "TN",
                    "doc_count": 23,
                    "average_balance": {
                        "value": 29796.782608695652
                    }
                },
                {
                    "key": "MA",
                    "doc_count": 21,
                    "average_balance": {
                        "value": 29726.47619047619
                    }
                },
                {
                    "key": "NC",
                    "doc_count": 21,
                    "average_balance": {
                        "value": 26785.428571428572
                    }
                },
                {
                    "key": "ND",
                    "doc_count": 21,
                    "average_balance": {
                        "value": 26303.333333333332
                    }
                },
                {
                    "key": "ME",
                    "doc_count": 20,
                    "average_balance": {
                        "value": 19575.05
                    }
                },
                {
                    "key": "MO",
                    "doc_count": 20,
                    "average_balance": {
                        "value": 24151.8
                    }
                }
            ]
        }
    }
}
```

## 首先按年龄分组，然后按性别分组 ，计算平均存款 range & terms
```
{
  "size": 0,
  "aggs": {
    "group_by_age": {
      "range": {
        "field": "age",
        "ranges": [
          {
            "from": 20,
            "to": 30
          },
          {
            "from": 30,
            "to": 40
          },
          {
            "from": 40,
            "to": 50
          }
        ]
      },
      "aggs": {
        "group_by_gender": {
          "terms": {
            "field": "gender.keyword"
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
  }
}
```
返回结果
```
{
    "took": 19,
    "timed_out": false,
    "_shards": {
        "total": 5,
        "successful": 5,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": 1000,
        "max_score": 0,
        "hits": []
    },
    "aggregations": {
        "group_by_age": {
            "buckets": [
                {
                    "key": "20.0-30.0",
                    "from": 20,
                    "to": 30,
                    "doc_count": 451,
                    "group_by_gender": {
                        "doc_count_error_upper_bound": 0,
                        "sum_other_doc_count": 0,
                        "buckets": [
                            {
                                "key": "M",
                                "doc_count": 232,
                                "average_balance": {
                                    "value": 27374.05172413793
                                }
                            },
                            {
                                "key": "F",
                                "doc_count": 219,
                                "average_balance": {
                                    "value": 25341.260273972603
                                }
                            }
                        ]
                    }
                },
                {
                    "key": "30.0-40.0",
                    "from": 30,
                    "to": 40,
                    "doc_count": 504,
                    "group_by_gender": {
                        "doc_count_error_upper_bound": 0,
                        "sum_other_doc_count": 0,
                        "buckets": [
                            {
                                "key": "F",
                                "doc_count": 253,
                                "average_balance": {
                                    "value": 25670.869565217392
                                }
                            },
                            {
                                "key": "M",
                                "doc_count": 251,
                                "average_balance": {
                                    "value": 24288.239043824702
                                }
                            }
                        ]
                    }
                },
                {
                    "key": "40.0-50.0",
                    "from": 40,
                    "to": 50,
                    "doc_count": 45,
                    "group_by_gender": {
                        "doc_count_error_upper_bound": 0,
                        "sum_other_doc_count": 0,
                        "buckets": [
                            {
                                "key": "M",
                                "doc_count": 24,
                                "average_balance": {
                                    "value": 26474.958333333332
                                }
                            },
                            {
                                "key": "F",
                                "doc_count": 21,
                                "average_balance": {
                                    "value": 27992.571428571428
                                }
                            }
                        ]
                    }
                }
            ]
        }
    }
}
```
