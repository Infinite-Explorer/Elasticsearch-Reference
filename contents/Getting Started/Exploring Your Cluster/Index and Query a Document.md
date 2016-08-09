## 索引并查询文档

现在我们放些数据到"customer"索引中去。回想之前为了索引一个文档，我们必须告诉ES它是属于索引中的哪个类型。

我们索引一个customer文档到customer索引中去，"external"类型，ID为1：

我们的JSON文档：{ "name": "John Doe" }

> **<pre>
curl -XPUT 'localhost:9200/customer/external/1?pretty' -d '
{
  "name": "John Doe"
}'
> </pre>**

响应：

> **<pre>
curl -XPUT 'localhost:9200/customer/external/1?pretty' -d '
{
  "name": "John Doe"
}'
{
  "_index" : "customer",
  "_type" : "external",
  "_id" : "1",
  "_version" : 1,
  "created" : true
}
> </pre>**

根据上面，我们可以看到一个新的customer文档在customer索引，external类型中被成功创建。该文档也包含一个自身在索引时指定的id 1。

记住ES在索引数据之前不需要先明确地先创建索引。在前面的例子中，如果不存在customer索引的话，ES会自动创建。

现在来读取我们刚刚索引的文档：

> **curl -XGET 'localhost:9200/customer/external/1?pretty'**

响应：

> **<pre>
curl -XGET 'localhost:9200/customer/external/1?pretty'
{
  "_index" : "customer",
  "_type" : "external",
  "_id" : "1",
  "_version" : 1,
  "found" : true,
  "_source" : { "name": "John Doe" }
}
> </pre>**

没有什么不同的地方。除了字段`found`，表示我们找到了ID为1的文档，和另一个字段`_source`,包含了我们前面索引文档的完整JSON文档。
