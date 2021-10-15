# 介绍
1. 集群上管理和运行容器的对象
## Pod和Controller关系
1. Pod是通过Controller实现应用的运维，例如伸缩，滚动升级等
2. Pod和Controller之间通过label建立关系， selector

## deployment应用场景
1. 部署无状态应用
2. 管理Pod和Replicaet
3. 部署、滚动升级等功能

应用场景： web服务，微服务

## yaml
command:
```shell
# 创建deployment
kubectl create deployment web --image=nginx -o yaml --dry-run > nginx.yaml
# 
kubectl apply -f nginx.yaml
# 对外发布,暴露对外端口
kubectk expose deployment web --port=80 --type=NodePort --target-port=80 --name=web1 -o yaml > web1.yaml
# 升级
kubectl set image deployment web nginx=nginx:1.15
## 查看升级
kubectl rollout status deployment web
kubectl rollout history deployment web

# 回滚上一个版本
kubectl rollout undo deployment web
## 回滚到指定版本
kubectl rollout undo deployment web --to-revision=2

# 弹性升级
kubectl scala deployment web --replicas=10

```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: web
  name: web
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: web
    spec:
      containers:
      - image: nginx
```

## 无状态和有状态
1. 无状态
   - 认为Pod都是一样的
   - 没有顺序要求
   - 不用考虑在那个node运行
   - 随意进行伸缩和扩展
2. 有状态
   - 上面这些因素均需要考虑到
   - 让每个Pod都是独立的，保持Pod启动顺序和唯一性
     - 唯一的网络标识符，持久存储
     - 有序，比如mysql主从
## 有状态应用部署
> 无头service： ClusterIP:none
1. SatefulSet部署
   - 查看pod，每一个pod都有唯一名称
   - 查看船舰的无头service
   - deployment和statefulset区别：有身份的（唯一标识）
     - 根据主机名 + 按照一定规则生成域名
     - 每个pod有唯一主机名
     - 唯一域名： 格式： 主机名称.{service}.{namespace}.svc.cluster.local
```
apiVersion: apps/v1beta1
kind: StatefulSet
metadata:
```

## Daemonset部署守护进程

> 在每个node上运行一个pod，新加入的node也同样运行在一个pod里面
```
apiVersion: apps/v1
kind: DaemonSet
metadata:
```
## job（一次性任务）

```
apiVersion: batch/v1
kind: Job
metadata:
```

## crontab（定时任务）
