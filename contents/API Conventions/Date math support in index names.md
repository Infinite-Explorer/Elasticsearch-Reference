### 索引名称支持日期数学

日期数学索引名称的解决方案是你能够搜索一个时间序列范围的索引。而不是搜索整个时间序列的索引，然后过滤结果或者维护别名。限制搜索的索引数量降低了集群的负载并提高了执行性能。例如，如果你在每天的日子中搜索错误信息，你可以使用日期数学模板将搜索限制在过去的两天内。

几乎所有的API都有`index`参数，并且`index`参数值支持日期数学。

日期数学索引名称格式如下：

> **<static_name{date_math_expr{date_format|time_zone}}>**

每部分的含义：

`static_name`

索引名称的固定部分

`date_math_expr`

动态计算日期的动态日期数学表达式。

`date_format`

计算出的日期可选的渲染格式。默认为`YYYY.MM.dd`。

`time_zone`

可选的时区。默认为`utc`。

你必须将日期数学表达式放入尖括号。例如：

> **<pre>
curl -XGET 'localhost:9200/<logstash-{now%2Fd-2d}>/_search' {
    "query" : {
      ...
    }
}
> </pre>**


#### 注意

用于日期取整的/符号必须在所有的url中被编码为`%2F`。

下面的例子展示了不同格式的日期数学索引名称及其最终被解析成的日期，假定目前时间是 utc时间2024年3月22日。

<table style="border-collapse:collapse;">
<tr>
<td>
表达式
</td>
<td>
解析后的日期
</td>
</tr>
<tr>
<td>
&lt;logstash-{now/d}&gt;
</td>
<td>
logstash-2024.03.22
</td>
</tr>
<tr>
<td>
&lt;logstash-{now/M}&gt;
</td>
<td>
logstash-2024.03.01
</td>
</tr>
<tr>
<td>
&lt;logstash-{now/M{YYYY.MM}}&gt;
</td>
<td>
logstash-2024.03
</td>
</tr>
<tr>
<td>
&lt;logstash-{now/M-1M{YYYY.MM}}&gt;
</td>
<td>
logstash-2024.02
</td>
</tr>
<tr>
<td>
&lt;logstash-{now/d{YYYY.MM.dd|+12:00}}&gt;
</td>
<td>
logstash-2024.03.23
</td>
</tr>
</table>


要想在索引名称模板中的固定部分使用字符`{`和`}`，需要使用反斜杠`\`转义它们，例如：

* `<elastic\\{ON\\}-{now/M}> resolves to elastic{ON}-2024.03.01`

下面的例子展示了一个搜索Logstash过去三天的索引的搜索请求，假设索引使用了默认的Logstash索引名称格式`logstash-YYYY.MM.dd`。

> **<pre>
curl -XGET 'localhost:9200/<logstash-{now%2Fd-2d}>,<logstash-{now%2Fd-1d}>,<logstash-{now%2Fd}>/_search' {
    "query" : {
      ...
    }
}
> </pre>**