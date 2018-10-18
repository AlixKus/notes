# 搜索多个索引
可以直接写成test1,test2,test3
也可以使用占位符*或-  
test*,-test3 表示 除了test3的test*
所有多索引API都支持以下url参数
- ignore_unavailable
是否忽略指定索引不可用，值为true或false
- allow_no_indices
当没有匹配到索引时，控制是否失败。为true或false。
- expand_wildcards

# 带日期的索引·
<static_name{date_math_expr{date_format|time_zone}}>
```
# GET /<logstash-{now/d}>/_search
GET /%3Clogstash-%7Bnow%2Fd%7D%3E/_search
{
  "query" : {
    "match": {
      "test": "data"
    }
  }
}
```

# 可选项
## 美化结果
?pretty=true 美化返回的JSON格式  
?format=yaml 直接返回yaml格式

## 人类易读格式
?human=false 默认值  
易读格式  (eg "exists_time": "1h" or "size": "1kb")
for 机器 (eg "exists_time_in_millis": 3600000 or "size_in_bytes": 1024)

## 计算日期
dateStiring`||`结尾   
now 现在  
+1h +1小时  
-1d 减一天  
/d 四舍五入到最近一天  
now+1 当前毫秒加上一小时  
y 年  
M 月  
w 周  
d 日  
h 小时  
H 小时  
m 分钟  
s 秒  
2001.02.01\|\|+1M/d  
2001-02-01加上一个月 结果为2001-03-01 00:00:00  
## 结果过滤  
`_filter_path`用于减少返回相应的字段,只返回包含的字段，可以使用`*`或`-`  
例如  
`GET /_search?q=elasticsearch&filter_path=took,hits.hits._id,hits.hits._score`
Responds:
```
{
  "took" : 3,
  "hits" : {
    "hits" : [
      {
        "_id" : "0",
        "_score" : 1.6375021
      }
    ]
  }
}
```

## Flat Settings 扁平结果
只有某些API可用  
`GET twitter/_settings?flat_settings=true`
```
{
    "bank": {
        "settings": {
            "index.creation_date": "1539755728392",
            "index.number_of_replicas": "1",
            "index.number_of_shards": "5",
            "index.provided_name": "bank",
            "index.uuid": "-E4CiaeYTyyHEdQxLdbP9w",
            "index.version.created": "6040299"
        }
    }
}
```
默认结果
```
{
    "bank": {
        "settings": {
            "index": {
                "creation_date": "1539755728392",
                "number_of_shards": "5",
                "number_of_replicas": "1",
                "uuid": "-E4CiaeYTyyHEdQxLdbP9w",
                "version": {
                    "created": "6040299"
                },
                "provided_name": "bank"
            }
        }
    }
}
```
