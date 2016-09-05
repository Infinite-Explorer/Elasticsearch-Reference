### 查询API

查询API能够通过id获取相应的已存入的JSON文档。下面例子将会从twitter索引，tweet类型下获取id为1的JSON文档：

> **curl -XGET 'http://localhost:9200/twitter/tweet/1'**

返回值为：

> **<pre>
{
    "_index" : "twitter",
    "_type" : "tweet",
    "_id" : "1",
    "_version" : 1,
    "found": true,
    "_source" : {
        "user" : "kimchy",
        "postDate" : "2009-11-15T14:12:12",
        "message" : "trying out Elasticsearch"
    }
}
> </pre>**

上面的返回值中包含了我们想要的关于文档的`_index`,`_type`,`_id`，如果能查询到（可从返回值的`found`字段看到结果）文档的话还会返回文档的实际`_source`值。

查询API还能使用`HEAD`方法检查某个文档是否存在，例如：

> **curl -XHEAD -i 'http://localhost:9200/twitter/tweet/1'**

### 实时

默认的，查询API是实时的，不受索引的刷新频率影响（当文档能被搜索的时候）。

为了禁用实时查询，你既可以设置`realtime`参数为`false`，也可以在节点配置中将`action.get.realtime`全局默认设置为`false`。

在查询文档的时候，你可以指定`fields`去获取文档。如果可能的话，指定的字段会作为存储字段（映射存储在映射中的字段）被获取。在使用实时的查询API时，没有存储字段的概念（至少是在一段时间内没有，基本上，直到下一次刷新），因此这些字段会从原始数据中提取（注意，即使没有开启source字段）。在使用实时查询API时假设这些字段会从原始数据中加载是一个好的做法，即使它们没有被存储。

### 可选选项

查询API中`_type`是可选的。为了获取在所有类型中匹配到id的第一个文档可以将其设置其为`_all`。

### 源过滤


