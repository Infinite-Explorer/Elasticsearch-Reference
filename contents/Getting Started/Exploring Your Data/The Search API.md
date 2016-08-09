## 查询API

我们从一些简单的查询开始。有两个基本方法执行查询：一个是通过[REST request URI](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-uri-request.html)传递查询参数放，另一个是通过[REST request body](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-body.html)传递。REST request body这种方式能够让查询目的更明确，使用JSON格式也增加了可读性。我们会在一个例子中试验request URI方法，教程后面的部分都会只使用request body方法。

通过`_search`端点访问搜索API。下面的例子会返回bank索引中的所有文档：

> **curl 'localhost:9200/bank/_search?q=*&pretty'**

我们首先仔细研究下查询的调用过程。我们在bank索引中进行搜索（`_search` 端点），`q=*`参数让ES匹配bank索引中的所有行。`pretty`参数让ES返回优雅打印的JSON结果。

返回内容（部分展示）

> **<pre>
curl 'localhost:9200/bank/_search?q=*&pretty'
{
  "took" : 63,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 1.0,
    "hits" : [ {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "1",
      "_score" : 1.0, "_source" : {"account_number":1,"balance":39225,"firstname":"Amber","lastname":"Duke","age":32,"gender":"M","address":"880 Holmes Lane","employer":"Pyrami","email":"amberduke@pyrami.com","city":"Brogan","state":"IL"}
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "6",
      "_score" : 1.0, "_source" : {"account_number":6,"balance":5686,"firstname":"Hattie","lastname":"Bond","age":36,"gender":"M","address":"671 Bristol Street","employer":"Netagy","email":"hattiebond@netagy.com","city":"Dante","state":"TN"}
    }, {
      "_index" : "bank",
      "_type" : "account",
> </pre>**

我们来看看返回内容：

* `took` - ES执行搜索所用的毫秒数
* `time_out` - 表示搜索是否超时
* `_shards` - 表示被搜索的分片数，以及成功和失败的分片数量
* `hits` - 搜索结果
* `hits.total` - 根据搜索条件匹配到的文档总数
* `hits.hits` - 实际返回的搜索结果数组（默认为前10个文档）
* `_score`和`max_score` - 暂时忽略这两个字段

现在使用request body方法进行和上面一样的精确搜索：

> **<pre>
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} }
}'
> </pre>**

跟request URI不同的是，我们传递一个JSON风格的查询请求体到 `_search` API，而不是在URI中写上`q=*`。我们会在下个章节讨论JSON查询。

返回内容（部分展示）

> **<pre>
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} }
}'
{
  "took" : 26,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 1.0,
    "hits" : [ {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "1",
      "_score" : 1.0, "_source" : {"account_number":1,"balance":39225,"firstname":"Amber","lastname":"Duke","age":32,"gender":"M","address":"880 Holmes Lane","employer":"Pyrami","email":"amberduke@pyrami.com","city":"Brogan","state":"IL"}
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "6",
      "_score" : 1.0, "_source" : {"account_number":6,"balance":5686,"firstname":"Hattie","lastname":"Bond","age":36,"gender":"M","address":"671 Bristol Street","employer":"Netagy","email":"hattiebond@netagy.com","city":"Dante","state":"TN"}
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "13",
> </pre>**

你应该需要牢记一旦你得到搜索返回结果，ES就完成了请求并且不会维持任何形式的服务端资源或者跟结果有某种联系。这跟许多其他的平台是完全相反的，例如在SQL里，你可能在开始只获得查询结果的部分子集，如果你想要获取(或者说分页获取)剩下的结果就需要继续向服务器使用某种带状态的服务端句柄。
