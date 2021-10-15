[](https://docs.fluentbit.io/manual/administration/configuring-fluent-bit/upstream-servers)
https://docs.fluentd.org/configuration/config-file
https://banzaicloud.com/docs/one-eye/logging-operator/

[fluentbit filter -- Kubernetes](https://docs.fluentbit.io/manual/pipeline/filters/kubernetes)
Fluent Bit Kubernetes Filter allows to enrich your log files with Kubernetes metadata.
filter目的执行以下操作：
- 分析tag并且提取以下metadata：
  - Pod Name
  - Namespace
  - Containter Name
  - Container ID
- 请求k8s API获取额外的metadata
  - Pod ID
  - Labels
  - Annotations