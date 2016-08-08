## 更新文档

除了能够索引和替换文档，我们也能更新文档。注意虽然ES事实上内部机制不是更新。更新的时候，ES对于匹配到需要更新的文档都是先删除就文档，再索引一个新文档。

下边的例子展示了如何在改变name字段值为"Jane Doe"之后更新我们之前的文档（ID为1）：

> **<pre>
curl -XPOST 'localhost:9200/customer/external/1/_update?pretty' -d '
{
  "doc": { "name": "Jane Doe" }
}'
> </pre>**

下面的例子展示了如何在改变name字段值为"Jane Doe"并同时增加一个之age字段之后更新我们之前的文档（ID为1）：

> **<pre>
curl -XPOST 'localhost:9200/customer/external/1/_update?pretty' -d '
{
  "doc": { "name": "Jane Doe", "age": 20 }
}'
> </pre>**

通过使用简单的脚本也能执行更新操作。注意像下面这样的动态脚本在1.4.3版本中是默认被禁用的，详情请看[脚本相关文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-scripting.html)。下面的例子使用脚本去是年龄加5。

在上面的例子中，ctx._source 引用的是当前将要被更新的原始文档。

注意，在本文档编写时，同一时间只能在一个单一的文档上进行更新操作。在将来，ES可能会提供根据查询条件更新多个文档的功能（就像SQL UPDATE-WHERE语句）。


