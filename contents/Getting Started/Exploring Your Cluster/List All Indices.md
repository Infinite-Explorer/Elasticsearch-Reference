## 列出所有索引

我们偷瞄一下索引：

> **'localhost:9200/_cat/indices?v'**

响应：

> **<pre>
curl 'localhost:9200/_cat/indices?v'
health index pri rep docs.count docs.deleted store.size pri.store.size
> </pre>**

这仅仅表明我们在集群中还没有索引存在。