## 常用选项

下面的选项可以应用在所有的ES REST API中。

### 优雅的结果

当在每一个请求后面加上`?pretty=true`时，JSON会以优雅的格式返回（只用来debug！）。另一个选项是设置`?format=yaml`，这会返回可读性更好（有时）的yaml格式。

### 人类可读输出

统计信息可返回适合人类的格式（例如`"exists_time": "1h"`或者`"size": "1kb"`）和适合电脑的格式（例如`"exists_time_in_millis": 3600000`或者`"size_in_bytes": 1024`）。人类可读格式可以通过在查询字符串后面加上`?human=false`来关闭。这在统计结果用于监控工具消费，而不是用于人类
的时候很有意义。

### 日期数学

大部分参数都接受格式化的日期值 —— 例如在[范围查询](https://www.elastic.co/guide/en/elasticsearch/reference/2.3/query-dsl-range-query.html)`range`中的`gt`和`lt`，或者在日期范围聚合中的`from`和`to` —— 理解日期数学。

数学表达式以一个固定日期开始，可以是`now`或者是以`||`结尾的日期字符串。固定日期后面可以跟上一个或多个下面的数学表达式：

* `+1h` - 增加一小时
* `-1d` - 减去一天
* `/d` - 向下取整最近的一天

支持的[时间单位](https://www.elastic.co/guide/en/elasticsearch/reference/2.3/common-options.html#time-units)有：`y` (年), `M` (月), `w` (周), `d` (日), `h` (时), `m` (分), and `s` (秒)。

部分示例：

`now+1h`

当前时间加上1小时，以毫秒计。

`now+1h+1m`

当前时间加上1小时1分钟，以毫秒计。

`now+1h/d`

目前的时间加上1小时，再向下取整最近的一天。

`2015-01-01||+1M/d`

`2015-01-01`加上1个月，再向下取整最近的一天。

### 响应过滤

所有的ES REST API都支持`filter_path`参数，可以用来减少ES返回的响应内容。该参数值用逗号将点号表示的过滤器列表隔开：

> **<pre>
curl -XGET 'localhost:9200/_search?pretty&filter_path=took,hits.hits._id,hits.hits._score'
{
	  "took" : 3,
	  "hits" : {
	    "hits" : [
	      {
	        "_id" : "3640",
	        "_score" : 1.0
	      },
	      {
	        "_id" : "3642",
	        "_score" : 1.0
	      }
	    ]
	  }
}
> </pre>**

也支持使用`*`通配符去匹配所有的字段或者字段名称的一部分：

> <pre>
curl -XGET 'localhost:9200/_nodes/stats?filter_path=nodes.*.ho*'
{
	  "nodes" : {
	    "lvJHed8uQQu4brS-SXKsNA" : {
	      "host" : "portable"
	    }
	  }
}
> </pre>

还能使用`**`通配符去包含不知道具体路径的字段。例如，我们可以使用下面的请求返回每个段的Lucene版本：

> <pre>
curl 'localhost:9200/_segments?pretty&filter_path=indices.**.version'
{
	  "indices" : {
	    "movies" : {
	      "shards" : {
	        "0" : [ {
	          "segments" : {
	            "_0" : {
	              "version" : "5.2.0"
	            }
	          }
	        } ],
	        "2" : [ {
	          "segments" : {
	            "_0" : {
	              "version" : "5.2.0"
	            }
	          }
	        } ]
	      }
	    },
	    "books" : {
	      "shards" : {
	        "0" : [ {
	          "segments" : {
	            "_0" : {
	              "version" : "5.2.0"
	            }
	          }
	        } ]
	      }
	    }
	  }
}
> </pre>

注意ES有时直接返回字段的原始值，例如`_source`字段。如果你想过滤`_source`字段，你应该考虑像下面这样结合已经存在的`_source`参数(更多详情请看[Get API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-get.html#get-source-filtering))和`filter_path`参数：

> **<pre>
curl -XGET 'localhost:9200/_search?pretty&filter_path=hits.hits._source&_source=title'
{
	  "hits" : {
	    "hits" : [ {
	      "_source":{"title":"Book #2"}
	    }, {
	      "_source":{"title":"Book #1"}
	    }, {
	      "_source":{"title":"Book #3"}
	    } ]
	  }
}
> </pre>**

### 扁平设置

`flat_settings`标志影响着设置列表的展示。当`flat_settings`标志设为`true`时,设置列表以扁平格式返回：

> **<pre>
{
    "persistent" : { },
    "transient" : {
      "discovery.zen.minimum_master_nodes" : "1"
    }
}
> </pre>**

当`flat_settings`标志设为`false`时，设置列表已人类可读的结构格式返回：

> **<pre>
{
	  "persistent" : { },
	  "transient" : {
	    "discovery" : {
	      "zen" : {
	        "minimum_master_nodes" : "1"
	      }
	    }
	  }
}
> </pre>**

`flat_settings`默认为`false`。

### 参数






