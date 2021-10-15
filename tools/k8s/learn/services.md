## 意义
1. 防止Pod失联， 进行服务发现


## Pod与Service关系

通过label和selector标签建立关系
通过service实现Pod的负载均衡

## 类型

1. ClusterIP 集群内部使用
2. NodePort 对外暴露
3. loadBalancer 对外访问应用使用，公有云