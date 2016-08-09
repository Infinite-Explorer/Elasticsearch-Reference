## 介绍查询语言

ES提供了可以执行查询的JSON风格的特定领域语言。这被称为[查询DSL](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html)。查询语言相当广泛，咋看之下会被吓到，但真正学习它的最好方式就是先尝试一些基础的例子。

回到我们上个例子，我们执行了查询：

> **<pre>
{
  "query": { "match_all": {} }
}
> </pre>**

仔细研究上面的内容，`query`部分定义了我们的查询，`match_all` 部分仅仅是我们想要执行的查询类型。`match_all`查询会查询指定索引中的所有文档。

除了`query`参数，我们还可以传其他的一些参数去影响查询结果。例如，下面执行了`match_all`操作并只返回了第一个文档：

> **<pre>
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "size": 1
}'
> </pre>**

记住如果`size`没有指定的话，默认为10。

下面的例子执行`match_all`查询并返回第11到20的文档：

> **<pre>
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "from": 10,
  "size": 10
}'
> </pre>**

`from`参数（从0开始）指定了从哪个文档开始，`size`参数制定了在开始文档之后返回的文档数量，默认为0。

下面的例子执行了`match_all`查询并按账户余额降序排列，返回前10（默认数量）个文档。

> **<pre>
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
 "query": { "match_all": {} },
  "sort": { "balance": { "order": "desc" } }
}'
> </pre>**