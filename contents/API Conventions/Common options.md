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


