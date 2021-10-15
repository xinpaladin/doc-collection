# Overview
Prometheus Operator 为 Kubernetes 提供 prometheus 的本地部署和管理并且与监控组件关联。项目目的是为 Kubernetes 集群简化与自动化基于prometheus监控栈的配置

特性（包括但不限于）：

  - Kubernetes Custom Resources: 使用Kubernetes custom resources部署管理Prometheus, Alertmanager, and related components.
  - 简化部署配置: Configure the fundamentals of Prometheus like versions, persistence, retention policies, and replicas from a native Kubernetes resource.
  - Prometheus Target Configuration: 根据熟悉的Kubernetes标签查询自动生成监控目标配置；无需学习Prometheus特定的配置语言。

# 前置
Version >=0.39.0 of the Prometheus Operator requires a Kubernetes cluster of version >=1.16.0. 

# CustomResourceDefinitions
监控 Kubernetes API server 获取特定objects的改变并且确保当前的Prometheus deployments 匹配这些objects
Operator 操作以下资源
- Prometheus 定义想要的Prometheus deployment
- Alertmanager 定义 Alertmanager deployment
- ThanosRuler 定义 Thanos Ruler deployment
- ServiceMonitor 显式的指定怎样的Kubernetes services groups应该被监控。Operator基于API server获取到的objects当前状态自动化生成Prometheus scrape 配置
- PodMonitor 显式的指定怎样的Kubernetes pods groups应该被监控。Operator基于API server获取到的objects当前状态自动化生成Prometheus scrape 配置
- Probe 显式的指定怎样的Kubernetes ingress或静态 targets groups应该被监控。Operator基于API server获取到的objects当前状态自动化生成Prometheus scrape 配置
- PrometheusRule 定义Prometheus alerting和recording rules集合。生成Prometheus使用的 rule file
- AlertmanagerConfig 声明性地指定了Alertmanager配置的子节，警报到自定义接收器的允许路由，并设置抑制规则。

Prometheus operator 自动检测以上