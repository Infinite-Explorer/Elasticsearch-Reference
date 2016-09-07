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

### 原始数据过滤

默认的，查询操作返回`_source`字段的内容，除非你使用了`fields`参数或者禁用了`_source`字段。你可以通过使用`_source`参数关掉`_source`字段的获取：

> **curl -XGET 'http://localhost:9200/twitter/tweet/1?_source=false'**

如果你仅仅是想从整个`_source`字段中获取一两个字段，你可以`_source_include`和`_source_exclude`参数去包含或者过滤筛选出你需要的部分。这对于部分获取可以节约网络带宽的大文档来说尤其有用。两个参数的值都是用逗号隔开的字段列表或者是通配符表达式。如下：

> curl -XGET 'http://localhost:9200/twitter/tweet/1?_source_include=*.id&_source_exclude=entities'

如果你仅仅想指定包含的字段，你可以使用的一个更加简短的表示法：

> **curl -XGET 'http://localhost:9200/twitter/tweet/1?_source=*.id,retweeted'**

### 字段

搜索操作可以通过传递`fields`参数返回一系列的存储字段。例如：

> **curl -XGET 'http://localhost:9200/twitter/tweet/1?fields=title,content'**

为了向后兼容，如果请求的字段没有被存储，它们会被从`_source`字段获取（解析和获取）。该功能已经被[原始数据过滤](https://www.elastic.co/guide/en/elasticsearch/reference/2.3/docs-get.html#get-source-filtering)参数替代。


从文档原始数据自身获取的字段值总是以数组的形式返回。元数据字段例如`_routing`和`_parent`字段绝不会以数组形式返回。

可以通过`field`选项使只返回叶字段。因此目标字段不能被返回，这样的请求也都会失败。

### 生成的字段

如果在索引和刷新之间没有发生刷新，查询操作会访问事务日志以获取文档。然而，一些字段只有在索引数据的时候才会生成。如果你试图访问一个只有在索引数据时才生成的字段，会发生异常（默认的）。如果通过设置`ignore_errors_on_generated_fields=true.`来访问事务日志，你就可以选择忽略该异常。

### 直接获取_source

使用`/{index}/{type}/{id}/_source`端点去获得文档的`_source`字段。不需要其他的任何内容。例如：

> **curl -XGET 'http://localhost:9200/twitter/tweet/1/_source'**

你也可以使用相同的原始数据过滤参数去控制返回`_source`字段的哪些部分：

> **curl -XGET 'http://localhost:9200/twitter/tweet/1/_source?_source_include=*.id&_source_exclude=entities'
**

注意，可以对于_sourcr端点可以使用HEAD方法有效检验文档是否存在。例如：

> **curl -XHEAD -i 'http://localhost:9200/twitter/tweet/1/_source'**


