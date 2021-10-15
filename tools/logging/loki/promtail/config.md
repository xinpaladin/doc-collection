Promtail Pipeline 日志处理配置

Promtail 配置文件说明

promtail 多行日志处理功能(multiline)需要 2.1.0 版本以上（2.1.0不行）

多行日志处理
```
promtail:
  enabled: true
  pipelineStages:
    - match:
        selector: '{logging="true"}'
        stages:
          - multiline:
              firstline: "^\\d{4}-\\d{2}-\\d{2}T\\d{2}:\\d{2}:\\d{2},\\d{3}"
              max_wait_time: 3s

```
解析日志添加label
```
     - match:
        selector: '{logging="true"}'
        stages:
          - regex: 
              expression: "(?P<time>\\d{4}-\\d{2}-\\d{2}T\\d{2}:\\d{2}:\\d{2},\\d{3})\\|(?P<level>\\w+)\\|(?P<thread_id>[^\\|]+)\\|(?P<process>[^\\|]+)\\|(?P<classname>[^\\|]+)\\|(?P<line>[^\\|]+)\\|(?P<message>[^\\|]+)"
          - labels:
              time: time
              level: level
              thread_id: thread_id
              process: process
              classname: classname
              line: line
              msg: msg
```

注意点：

正则格式 , 必须严格遵守下面的格式
```
expression: \w*
expression: '\w*'
expression: "\\w*"
```