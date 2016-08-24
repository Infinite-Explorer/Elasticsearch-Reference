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