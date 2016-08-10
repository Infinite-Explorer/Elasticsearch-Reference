## Executing Aggregations

聚合能够分组你的数据并且从中获得统计信息。最简单的理解聚合的方式就是想着跟SQL GROUP BY 和 SQL的聚合函数略同。在ES里，你可以执行在响应内容中同时返回命中文档和跟命中文档分开存储的聚合结果的搜索。使用简洁的API在一次请求中同时查询和多个聚合时同时返回两者匹配的内容（或者只返回其中一个）从而避免网络带宽消耗的意义上来讲是非常强大，有效的。

我们开始吧，下面的例子通过州对所有账户进行分组，并以数量降序排列返回前面10个州（默认）：

> **<pre>
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state"
      }
    }
  }
}'
> </pre>**

在SQL里面，上面的聚合跟下面概念上讲差不多：

> **SELECT state, COUNT(*) FROM bank GROUP BY state ORDER BY COUNT(*) DESC**

返回内容（部分显示）：

> **<pre>
"hits" : {
    "total" : 1000,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "group_by_state" : {
      "buckets" : [ {
        "key" : "al",
        "doc_count" : 21
      }, {
        "key" : "tx",
        "doc_count" : 17
      }, {
        "key" : "id",
        "doc_count" : 15
      }, {
        "key" : "ma",
        "doc_count" : 15
      }, {
        "key" : "md",
        "doc_count" : 15
      }, {
        "key" : "pa",
        "doc_count" : 15
      }, {
        "key" : "dc",
        "doc_count" : 14
      }, {
        "key" : "me",
        "doc_count" : 14
      }, {
        "key" : "mo",
        "doc_count" : 14
      }, {
        "key" : "nd",
        "doc_count" : 14
      } ]
    }
  }
}
> </pre>**

我们看到有21个账户在AL(abama)州，接着是17个账户在TX州，15个账户在ID(aho)州等等。

注意因为我们只想看到聚合结果，所以设置`size=0`。

继续在上面的聚合中增加内容，下面的例子根据州计算平均账户余额（还是只返回按数量降序排列的前10个州）：

> **<pre>
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state"
      },
      "aggs": {
        "average_balance": {
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  }
}'
> </pre>**

注意我们在`group_by_state`聚合中嵌入了`average_balance`聚合。这是所有聚合通用的模式。你可以通过嵌入聚合到任意聚合去从你的数据中获得更多你需要的更小粒度的聚和信息。

继续在前面的聚合中增加内容，我们现在按平均账户余额降序排列：

> **<pre>
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state",
        "order": {
          "average_balance": "desc"
        }
      },
      "aggs": {
        "average_balance": {
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  }
}'
> **</pre>

下面的例子演示了我们如何通过年龄段分组(20-29，30-39，40-49)，然后通过性别分组，最后得到每个年龄段下每个性别的平均账户余额：

> **<pre>
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "size": 0,
  "aggs": {
    "group_by_age": {
      "range": {
        "field": "age",
        "ranges": [
          {
            "from": 20,
            "to": 30
          },
          {
            "from": 30,
            "to": 40
          },
          {
            "from": 40,
            "to": 50
          }
        ]
      },
      "aggs": {
        "group_by_gender": {
          "terms": {
            "field": "gender"
          },
          "aggs": {
            "average_balance": {
              "avg": {
                "field": "balance"
              }
            }
          }
        }
      }
    }
  }
}'
> </pre>**

还有很多其他我们不会在这里细讲的聚合功能。如果你想做更多的试验，[聚合参考指南](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html)是一个好的出发点。










