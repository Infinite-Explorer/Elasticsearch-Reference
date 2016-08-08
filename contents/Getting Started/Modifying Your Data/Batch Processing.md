## 批量处理

除了 能够索引，更新，删除单个文档，ES也提供了通过使用[_bulk API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-bulk.html)批量执行上述操作的功能。这个功能在提供尽快和尽少的带宽消耗去执行多个操作非常有效的机制中很重要。

来看一个快速的例子，会在一个bulk操作中索引2个文档（ID为1的John Doe，ID为2的Jane Doe）：

> **<pre>
curl -XPOST 'localhost:9200/customer/external/_bulk?pretty' -d '
{"index":{"_id":"1"}}
{"name": "John Doe" }
{"index":{"_id":"2"}}
{"name": "Jane Doe" }
'
> </pre>**

下边的例子会在一个bulk操作中更新第一个文档（ID为1的文档），然后删除第二个文档（ID为2的文档）：

> **<pre>
curl -XPOST 'localhost:9200/customer/external/_bulk?pretty' -d '
{"update":{"_id":"1"}}
{"doc": { "name": "John Doe becomes Jane Doe" } }
{"delete":{"_id":"2"}}
'
> </pre>

在上面的删除操作里，后面没有相应的源文档，这是因为删除操作只需要被删除的文档ID即可。

bulk API按顺序执行所有的操作。如果一个操作因为某种原因失败了，会继续执行其之后剩下的操作。当bulK API执行完返回时，内容会包含每一个操作的状态（跟请求是的顺序一致）以便你可以查看某一个操作失败与否。