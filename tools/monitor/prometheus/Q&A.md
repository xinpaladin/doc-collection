# 监控使用过程中的问题



## 告警时间
配置：
```yaml
# prometheus rule
alert: node_cpu_total_warn_test
annotations:
  summary: 'Host high CPU load (instance {{ $labels.instance }})'
expr: (avg by(instance)(rate(node_cpu_seconds_total{mode="idle"}[2m])) * 100) < 50
for: 30s
labels:
  role: test
  severity: warning

#alertmanager config
group_wait: 30s   
```
告警过程：
1. prometheus 采集数据(scrape_interval), 计算告警规则表达式(evaluation_interval) 
2. 当条件满足触发条件时，由于设置 for: 30s , alert处于  PENDING
3. 满足告警条件持续30秒( for: 30s),alert 处于 FIRING， 告警发送给 alertmanager。直到计算周期为假，alert会变为 inactive. resolve 发送个体 alertmanager
4. alertmanager 接受到告警后， 进入告警路由(group_wait: 30s),等待30s 后发送告警信息
