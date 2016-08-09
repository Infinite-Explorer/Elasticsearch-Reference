##执行过滤

在前面的章节，我们跳过了文档分数的细节（查询结果的`_score`字段）。分数是文档跟我们的搜索查询匹配相关度的对应数值。分数越高，文档相关度越高，分数越低，文档相关度越低。

然而查询不总是需要计算分数，尤其是当它们只是用来过滤文档集的时候。ES为了不产生无用的分数会检测实际情况并自动优化查询执行。

我们在前面章节介绍的[`bool`查询](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-bool-query.html)也支持`filter`子句，可以使用查询去限制被其他子句匹配到的文档，并且不改变分数的计算方式。作为例子，我们介绍下[`range`查询](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-range-query.html)，它通过范围值过滤文档。这通常会在数值或者日期过滤中用到。

下面的例子使用了bool查询获得所有账户余额介于20000到30000的账户，包含20000和30000。换句话说，我们想查找余额大于等于20000，小于等于30000的账户。

> **<pre>
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": {
    "bool": {
      "must": { "match_all": {} },
      "filter": {
        "range": {
          "balance": {
            "gte": 20000,
            "lte": 30000
          }
        }
      }
    }
  }
}'
> </pre>**

细看上边内容，bool查询包含了一个`match_all`查询（在query部分）和一个`range`查询(在filter部分)。我们可以使用任何其他的查询替换掉query和filter部分。在上面的例子中，由于文档在范围内被匹配到的几率一样,所以范围查询合情合理。

除了`match_all`,`match`,`bool`,`range`查询，还有很多其他可用的查询类型，我们不会再这里用到它们。由于我们已经对于它们如何工作已经有了基本的了解，所以学习和尝试其他查询类型应该不会太难。

