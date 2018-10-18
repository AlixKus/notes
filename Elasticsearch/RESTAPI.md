# REST API
## 查看类API
### 查看集群状态
`GET /_cat/health?v`
### 查看节点状态
`GET /_cat/nodes?v`
### 查看索引
`GET /_cat/indices?v`
### 获取文档
`GET /<Index>/<Type>/<ID>`

## 创建类API
通常是PUT动作
### 创建索引
`PUT /<Index>``
### 创建索引/替换文档
```
PUT /<Index>/<Type>/<ID>
{
	"name":"ddd"
}
```
自动生成ID的方式
```
POST /<Index>/<Type>
{
	"name":"ddd"
}
```
### 批量插入
```
POST /<Index>/<Type>/_bulk
{"index":{"_id":"1"}}
{"name": "John Doe" }
{"index":{"_id":"2"}}
{"name": "Jane Doe" }
```
>bulk不会因为其中一个操作失败而不处理后续操作。

## 更新类API
### 更新文档
```
POST /<Index>/<Type>/_update?pretty
{
  "doc": { "name": "Jane Doe" }
}
```
### 批量更新
```
POST /<Index>/<Type>/__bulk?pretty
{"update":{"_id":"1"}}
{"doc": { "name": "John Doe becomes Jane Doe" } }
{"update":{"_id":"2"}}
{"doc": { "name": "John Doe becomes Jane Doe2" } }
```

## 删除API
DELETE动作
### 删除索引
`DELETE /<Index>?pretty`
### 删除文档
`DELETE /<Index>/<Type>/<ID>?pretty`
### 批量删除
注意删除每一条只由一行构成
```
POST /<Index>/<Type>/__bulk?pretty
{"delete":{"_id":"2"}}
```
