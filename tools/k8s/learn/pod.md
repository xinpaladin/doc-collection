## 基本概念
1. 最小部署单元
2. 容器集合(一个或多个容器)
3. 一个pod中容器共享网络命名空间
4. pod是短暂的

## 存在意义
1. 创建容器使用docker，一个docker创建一个容器，一个容器是一个进程，一个容器运行一个应用程序
2. Pod是多进程设计，运行多个应用程序
   - 一个Pod有多个容器，一个容器运行一个应用程序
3. Pod存在为了亲密性应用
   - 两个应用之间进行交互
   - 网络之间调用
   - 两个应用需要频繁调用 


## Pod实现机制 
1. 共享网络
   通过Pause容器，把其他业务容器加入到Pause容器里面，让所有业务容器在同一个名称空间中，可以实现网络共享
2. 共享存储
   引入数据卷概念Volumn，使用数据卷进行持久化存储

## 镜像拉取策略
- IfNotPresent 
- Always
- Never

## 资源限制



## Pod 重启策略
- Always
- OnFailure 容器异常退出（推出状态马非0）
- Never 容器终止推出，从不重启容器

## 健康检查



## Pod调度
- node 资源
- nodeSelector
- nodeAffinity
  - 硬亲和性： 约束条件必须满足
    - requiredDuringSchedulingIgnoredDuringExecution
  - 软亲和性： 尝试满足，不保证
    - preferredDuringSchedulingIgnoredDuringExecution
- 污点（Taint）和污点容忍
  - 场景
    - 专用节点
    - 配置特点硬件节点
    - 基于Taint驱逐
  - 命令
    - 查看： kubectl describe node k8s-worker-1 |grep Taint
    - 添加： kubectl taint node k8s-worker-1 env_role=yes:NoSchedule
    - 删除： kubectl taint node k8s-worker-1 env_role=yes:NoSchedule-
  - 污点值
    - NoSchedule 一定不被调度
    - PreferNoSchedule 尽量不被调度
    - NoExecute 不会调度，并且会驱逐Node已有Pod
  - 污点容忍