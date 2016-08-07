## 创建索引

我们创建一个名为"customer"的索引，并再次列出所有索引：

> **<pre>
curl -XPUT 'localhost:9200/customer?pretty'
curl 'localhost:9200/_cat/indices?v'
> </pre>**

第一个命令使用PUT动词创建了"customer"索引。我们简单地在调用端点后面添加`pretty`使其优雅打印返回的JSON（如果返回的是JSON的话）。

响应：

> **<pre>
curl -XPUT 'localhost:9200/customer?pretty'
{
  "acknowledged" : true
}
<p>
curl 'localhost:9200/_cat/indices?v'
health index    pri rep docs.count docs.deleted store.size pri.store.size
yellow customer   5   1          0            0       495b           495b
> </pre>**

第二个命令的结果告诉我们现在有一个"customer"索引，其有5个主分片和一个副本（默认的），并且没有文档。

你或许也注意到"customer"索引是黄色健康状态。回想下我们之前的讨论，黄色意味着有些副本还未被分配。索引出现这个的原因是ES为索引默认创建一个副本。由于我们只有此时只有一个节点在运行，副本久不能被分配（为了高可用性）直到另一个节点加入到集群中的时候。一旦副本被分配到另一个节点，节点的健康状态会变成绿色。