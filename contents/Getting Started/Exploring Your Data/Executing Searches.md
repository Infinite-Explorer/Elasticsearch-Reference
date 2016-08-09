## 执行搜索

我们已经看到了一些基本的查询参数，让我们再深入研究下查询 DSL。我们先看下返回文档的字段。默认的，全JSON文档作为全部搜索结果的一部分返回。这被称作source（查询结果hits部分中的的`_source`字段）。如果我们不想返回整个source文档，我们可以只请求在返回source中的部分字段。

下面的例子展示了如何只返回`account_number`和`balance`（存在于`_source`字段内部）两个字段，搜索写法如下：

> **<pre>
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "_source": ["account_number", "balance"]
}'
> </pre>**

注意上面的例子仅仅减少了`_source`字段包含的内容。仍然只会返回一个`_source`字段，只是包含`account_number`和`balance`两个字段罢了。

如果你有SQL背景知识，上面所讲到的就跟`SQL SELECT FROM`的字段列表类似。

现在我们继续查询部分。就在之前，我们已经了解到`match_all`查询是如何被用来匹配所有文档的。现在我们介绍一个叫做[匹配查询](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-match-query.html)的新查询方式，可以被认为是一种基本的字段搜索查询（即：依靠特定的一个或多个字段进行查询）。

下面的例子返回了用户数量为20的文档：

> **<pre>
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match": { "account_number": 20 } }
}'
> </pre>**

下面的例子返回了所有地址包含"mill"的账户：

> **<pre>
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match": { "address": "mill" } }
}'
> </pre>**

下面的例子返回所有地址包含"mill"或者"lane"的账户：

> **<pre>
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match": { "address": "mill lane" } }
}'
> </pre>**

下面的例子是`match`的变种（match_phrase）,会返回所有地址包含"mill lane"短语的账户：

> **<pre>
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_phrase": { "address": "mill lane" } }
}'
> </pre>**

我们现在介绍下[`bool`(ean)查询](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-bool-query.html)。`bool`查询允许我们通过布尔逻辑将小的查询组合大的查询。

下面的例子组合了两个`match`查询并返回了所有地址包含"mill"和"lane"的账户：

> **<pre>
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": {
    "bool": {
      "must": [
        { "match": { "address": "mill" } },
        { "match": { "address": "lane" } }
      ]
    }
  }
}'
> </pre>**

在上面的例子中，`bool should`子句要求文档必须至少满足其下所有的匹配条件之一才能算匹配。

在下面的例子中组合了两个`match`查询并返回所有地址不包含"mill"和"lane"的账号：

> **<pre>
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": {
    "bool": {
      "must_not": [
        { "match": { "address": "mill" } },
        { "match": { "address": "lane" } }
      ]
    }
  }
}'
> </pre>**

在上面的例子中，`bool must_not` 子句要求文档必须其下的所有匹配条件都不满足才能匹配。

我们可以在一个`bool`查询中同时包含`must`,`should`,`must_bot`从句。

此外，我们可以将`bool`查询放到其他的`bool`查询子句中去构成任意复杂程度的布尔逻辑。

> **<pre>
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
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
}'
> </pre>**