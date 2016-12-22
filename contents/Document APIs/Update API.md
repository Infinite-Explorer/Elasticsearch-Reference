### 更新 API

更新API能够通过脚本更新文档。该操作首先从索引中获取文档（来自不同分片），然后执行脚本（使用可选的脚本语言及参数），然后将结果索引回去（也可以删除或者忽略该操作）。更新API通过使用版本控制以确保在执行“get”与“reindex”操作的时候不执行更新操作。

注意，更新操作还是会使文档完全重新索引，只不过是减少了网络消耗及get与reindex操作间发生版本冲突的几率。。只有启用`_source`字段才能使用更新API。

下面的例子中我们将索引一个简单的文档：

> **<pre>
curl -XPUT localhost:9200/test/type1/1 -d '{
    "counter" : 1,
    "tags" : ["red"]
}'
> </pre>**

## 使用脚本进行更新

现在我们可以执行一个脚本来增加counter的值：

> **<pre>
curl -XPOST 'localhost:9200/test/type1/1/_update' -d '{
    "script" : {
        "inline": "ctx._source.counter += count",
        "params" : {
            "count" : 4
        }
    }
}'
> </pre>**

我们可以添加一个标签到标签列表（注意，如果标签已经存在，仍然会被加入到列表中去，这是由于列表的特性所致）：

> **<pre>
curl -XPOST 'localhost:9200/test/type1/1/_update' -d '{
    "script" : {
        "inline": "ctx._source.tags += tag",
        "params" : {
            "tag" : "blue"
        }
    }
}'
> </pre>**

除了`字段`，下面的变量也可以通过`ctx`进行调用:`_index`,`_type`,`_id`,`_version`,`_routing`,`_parent`,`_timestamp`,`_ttl`。

我们还可以添加一个新的字段到文档中去：

> **<pre>
curl -XPOST 'localhost:9200/test/type1/1/_update' -d '{
    "script" : "ctx._source.name_of_new_field = \"value_of_new_field\""
}'
> </pre>**

或者从文档中移除一个字段：

> **<pre>
curl -XPOST 'localhost:9200/test/type1/1/_update' -d '{
    "script" : "ctx._source.remove(\"name_of_field\")"
}'
> </pre>**

我们甚至可以修改被执行的操作。下面的例子中，如果`tags`字段存在值`blue`就删除该文档，否则不进行任何操作(`noop`):

> **<pre>
curl -XPOST 'localhost:9200/test/type1/1/_update' -d '{
    "script" : {
        "inline": "ctx._source.tags.contains(tag) ? ctx.op = \"delete\" : ctx.op = \"none\"",
        "params" : {
            "tag" : "blue"
        }
    }
}'
> </pre>**
