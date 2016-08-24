## 常用选项

下面的选项可以应用在所有的ES REST API中。

### 优雅的结果

当在每一个请求后面加上`?pretty=true`时，JSON会以优雅的格式返回（只用来debug！）。另一个选项是设置`?format=yaml`，这会返回可读性更好（有时）的yaml格式。

### 人类可读的输出

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

余下的参数（使用HTTP的时候，映射为HTTP URL参数）都遵循使用带下划线的形式。

### 布尔值

所有的REST API参数（请求参数和JSON请求体）都支持使用`false`，`0`,`no`,`off`表示布尔值"false"。其他的值都表示"true"。注意，这跟文档中的字段是否被当做布尔类型字段进行索引无关。

### 数值

所有的REST API都支持在原生JSON的数字类型字段上使用数值字符串参数。

### 时间单位

无论何时在需要指定时长的时候，例如`timeout`参数，时长必须指定单位，例如2天用`2d`表示。支持的单位如下：

`y` 年

`M` 月

`w` 周

`d` 日

`h` 时

`m` 分

`s` 秒

`ms` 毫秒

###数据大小单位

无论何时在需要指定数据大小的时候，例如在设置一个缓冲大小的参数时，参数值必须指定单位，例如10千字节用`10kb`表示。支持的单位如下：

`b` 字节

`kb` 千字节

`mb` 百万字节

`gb` 十亿字节

`tb` 万亿字节

`pb` 千万亿字节

### 距离单位

无论何时在需要指定长度的时候，例如在[地理距离查询](https://www.elastic.co/guide/en/elasticsearch/reference/2.3/query-dsl-geo-distance-query.html)中用到的`distance`参数，如果没有指定，默认单位为米。也可以使用其他距离单位，例如`"1km"`或者`"2mi"`(2英里)。

全部距离单位如下所示：

英里 `mi`或者`miles`

码 `yd`或者`yards`

英尺 `ft`或者`feet`

英寸 `in`或者`inch`

公里 `km`或者`kilometers`

米 `m`或者`meters`

厘米 `cm`或`centimeters`

毫米 `mm`或`millimeters`

纳米 `NM`,`nmi`或者`nauticalmiles`

[地理哈希单元格查询](https://www.elastic.co/guide/en/elasticsearch/reference/2.3/query-dsl-geohash-cell-query.html)中的`precision`参数可以使用上面的距离单位，如果未指定单位，则精度被解释为地理哈希的长度。

### 模糊

一些查询和API使用`fuzziness`参数来支持不明确的模糊匹配。`fuzziness`参数是上下文敏感的，这意味着模糊查询取决于查询字段的数据类型：

#### 数字，日期和IPV4字段

查询数字，日期和IPV4字段的时候，`fuzziness`被解释为`+/-`边界。跟范围查询差不多：

> **-fuzziness <= field value <= +fuzziness**

`fuzziness`参数应设置为数值，例如`2`或者`2.0`。`date`字段将长整型数值当做毫秒处理，也可以使用时间字符串 — `"1h"` —在本章的"时间单位"部分已经解释过。`ip`字段可以使用长整型数值或者IPV4地址（会被转换成长整型数值）。

#### 字符串字段

查询`string`字段的时候，`fuzziness`被解释为[Levenshtein编辑距离](http://en.wikipedia.org/wiki/Levenshtein_distance) —— 将字符串转换为另一个字符串，字符所需要改变的次数。`fuzziness`参数可以设置为：

`0`,`1`,`2`

被允许的最大Levenshtein编辑距离（或者是编辑数量）

`AUTO`

根据条件长度产生一个编辑距离，长度有：

* `0..2`

　　必须精确匹配

* `3..5`

　　允许1个编辑距离

* `>5`

　　允许2个编辑距离

`AUTO`通常应该作为`fuzziness`的首选值。

### 结果命名

所有的ES REST API都能适应`case`参数。当设为`camelCase`时，返回结果中的字段名称都为驼峰格式，否则为下划线格式。注意，这不适用于索引的源文档。

### 查询字符串中的请求体

对于不接受非POST请求的请求体的图书馆查询系统，你可以通过查询字符串参数`source`来进行传递。




      

    















