# logQL
[官方文档](https://grafana.com/docs/loki/latest/logql/)
## Log Queries
### Log stream selector
日志流选择器这部分和PromQL的语法一样，主要也是通过采集上来的日志label来确定你要查询的日志流。通常label的匹配运算支持以下几种：

- =: 完全匹配
- !=: 不匹配
- =~: 正则表达式匹配
- !~: 正则表达式不匹配
例如:
```
{name=~"mysql.+"}
{name!~"mysql.+"}
{name!~`mysql-\d+`}
```
## Log Pipeline
### Line Filter Expression
filter 操作符

- |=:  日志行包含
- !=:  不包含
- |~:  匹配正则
- !~:  不匹配正则
例如：
```
{job="mysql"} |= "error"
{name="kafka"} |~ "tsdb-ops.*io:2003"
{name="cassandra"} |~ `error=\w+`
{instance=~"kafka-[23]",name="kafka"} != "kafka.server:type=ReplicaManager"
```
### Parser Expression
解析转换日志格式

- json 将json格式的字符串解析，详见官网
- logfmt 
- regexp 正则表达式匹配日志内容，解析匹配字段
- unpack
### Label Filter Expression
过滤日志使用原始与提取出的labels，支持的数据类型 String/Duration/Number/Bytes

- == or = 
- !=  
- > and >= 
- < and <= 
- Line Format Expression
重新组织日志内容

{container="frontend"} | logfmt | line_format "{{.query}} {{.duration}}"
Labels Format Expression
重命名，修改，添加 labels

### Unwrap Expression


## Metric Queries
### Range Vector aggregation 
区间向量，与prometheus 相同

#### Log Range Aggregations
- rate: 计算每秒的日志条目
- count_over_time: 对指定范围内的每个日志流的条目进行计数
- bytes_rate: 计算日志流每秒的字节数
- bytes_over_time: 对指定范围内的每个日志流的使用的字节数
- absent_over_time  有元素返回空，否则返回1
#### Unwrapped Range Aggregations
它的主要作用在于根据过滤表达式提取一个时间区间内的值进行聚合查询。简单来说，以前LogQl这部分只能对日志行进行聚合查询，现在也能对日志内容做聚合查询了。它支持了如下的函数：

- duration_seconds(label_identifier) (or its short equivalent duration) which will convert the label value in seconds from the go duration format (e.g 5m, 24s30ms).
- bytes(label_identifier)  将label value转换为raw bytes使用bytes 单位 (e.g. 5 MiB, 3k, 1G).

Supported function for operating over unwrapped ranges are:

- rate(unwrapped-range): calculates per second rate of all values in the specified interval.
- sum_over_time(unwrapped-range)：指定时间间隔内所有值的总和。
- avg_over_time(unwrapped-range)：指定间隔内所有点的平均值。
- max_over_time(unwrapped-range)：指定间隔内所有点的最大值。
- min_over_time(unwrapped-range)：指定间隔中所有点的最小值
- stdvar_over_time(unwrapped-range)：指定间隔内值的总体标准方差。
- stddev_over_time(unwrapped-range)：指定间隔内值的总体标准偏差。
- quantile_over_time(scalar,unwrapped-range)：指定间隔内值的φ分位数（0≤φ≤1）
- absent_over_time(unwrapped-range): 
### Aggregation operators
- sum：求和
- min：最小值
- max：最大值
- avg：平均值
- stddev：标准差
- stdvar：标准方差
- count：计数
- bottomk：最小的k个元素
- topk：最大的k个元素