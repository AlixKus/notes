## GET START
搜索所有文档并按照account_number:asc排序  
`GET /bank/_search?q=*&sort=account_number:asc&pretty`  
返回值：
```
{
  "took" : 63,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : null,
    "hits" : [ {
      "_index" : "bank",
      "_type" : "_doc",
      "_id" : "0",
      "sort": [0],
      "_score" : null,
      "_source" : {"account_number":0,"balance":16623,"firstname":"Bradshaw","lastname":"Mckenzie","age":29,"gender":"F","address":"244 Columbus Place","employer":"Euron","email":"bradshawmckenzie@euron.com","city":"Hobucken","state":"CO"}
    }, {
      "_index" : "bank",
      "_type" : "_doc",
      "_id" : "1",
      "sort": [1],
      "_score" : null,
      "_source" : {"account_number":1,"balance":39225,"firstname":"Amber","lastname":"Duke","age":32,"gender":"M","address":"880 Holmes Lane","employer":"Pyrami","email":"amberduke@pyrami.com","city":"Brogan","state":"IL"}
    }, ...
    ]
  }
}
```
took ElasticSearch搜索所用毫秒数  
timed_out 搜索是否超时  
_shards 搜索了多少个分片，以及搜索成功/失败的分片计数。  
hits 搜索结果  
hits.total 符合搜索条件的文档总数  
hits.hits 实际的搜索结果数组（默认为前十个文档）  
hits.sort 结果排序  
一旦返回结果，Elasticsearch就完成了搜索请求。区别于SQL中游标。
