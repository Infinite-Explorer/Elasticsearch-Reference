### 索引API

索引API使用输入的JSON文档在指定的索引中添加或更新，以使该文档能够被搜索到。在下面的例子中，将JSON文档插入到了"twitter"索引，"tweet"类型下，并且id为1：

> **<pre>
$ curl -XPUT 'http://localhost:9200/twitter/tweet/1' -d '{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}'
> </pre>**

上面所有操作的返回结果如下：

> **<pre>
{
    "_shards" : {
        "total" : 10,
        "failed" : 0,
        "successful" : 10
    },
    "_index" : "twitter",
    "_type" : "tweet",
    "_id" : "1",
    "_version" : 1,
    "created" : true
}
> </pre>**

`_shards`头提供了索引操作的复制过程的信息。

* `total` - 表示了索引操作需要在多少分片拷贝上执行。
* `successful` - 表示索引操作执行成功的分片拷贝数量。
* `failed` - 包含在副本分片上索引操作失败时，复制相关错误的数组。

索引操作在`successful`字段值大于等于1时，即表示成功。

#### 注意

在索引操作成功返回（默认必须规定数量）的时候，可能并不是所有的副本分片都已经启动了的。在这种情况下，`total`字段值跟索引副本设置的总分片数一致，`successful`字段值跟启动的分片数（主分片加上副本分片）一致。由于没有失败发生，`failed`字段值为0。

### 自动索引创建

索引操作在索引还未创建之前（查看创建[索引API](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-create-index.html)）会自动创建，如果指定的类型还未创建映射（查看设置映射API，了解如何手动创建[类型映射](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-put-mapping.html)）就会自动创建一个动态类型映射。

映射自身是很灵活和模式自由的。新的字段和对象会自动加入到指定类型的映射定义中去。查看映射部分以获得关于映射定义的更多信息。

自动索引创建可以通过在所有节点的配置文件中将`action.auto_crete_index`设为`false`来禁用掉。自动映射创建可以通过在所有节点的配置中将`index.mapper,dynamic`设置为`false`来禁用掉（或者是在指定的索引设置中进行设置）。

自动索引创建可以引入基于模式的黑白名单，例如，将`action.auto_create_index`设为`+aaa*,-bbb*,+ccc*,-*`（+表示运行，-表示禁止）。

### 版本控制

每一个被索引的文档都有一个版本号。相关联的`version`号作为索引API请求的响应内容的一部分返回。索引API在指定`version`参数的时候可允许乐观并发控制。这会控制将要执行操作的文文档的版本。一个关于版本控制好的用例是执行读取然后更新的事务操作。在开始读取文档的时候指定`version`可以确保同时不会出现变动（当为了之后的更新而读取的时候，建议将`perference`改为`_primary`）。例如：

> **<pre>
curl -XPUT 'localhost:9200/twitter/tweet/1?version=2' -d '{
    "message" : "elasticsearch now has versioning support, double cool!"
}'
> </pre>**

**注意**：版本控制是完全实时的，并且不会受到搜索操作准实时方面的影响。如果不提供版本，操作执行的时候不会进行版本检查。

默认的，内部的版本号从1开始计算，并且在每次的更新，删除操作的时候加1。