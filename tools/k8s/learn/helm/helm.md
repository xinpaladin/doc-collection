# helm

## helm 引入
### 原生部署应用过程
- 编写yaml
  - deployment
  - service
  - ingress

缺点：
1. 部署微服务项目，可能有即使个服务，yaml配置文件很多，维护不方便，版本管理不方便

### helm解决了那些问题
1. 使用helm可以把yaml作为一个整体管理
2. 实现yaml高效复用
3. 实现应用级别的版本管理


## 介绍
类似与包管理工具，app store

## 概念
1. helm
命令行客户端工具
2. Chart
把yaml打包，是yaml集合。
3. release
基于chart的部署实体，应用级别的版本管理

## helm v2 vs v3
1. v3删除了Tiller
2. release v3支持在不同namespace中重用
3. chart可以推送到docker仓库中

## 架构
helm chart -> tiller -> kube-apiserver -> deployment/service/...
v3:
helm chart -> kube-config -> kube-apiserver -> deployment/service/...

## 命令
### 仓库管理


### 快速部署应用


### 自创建Chart
1. 创建初始模板
   ```
   helm create chart
   ```
   模板架构：
   - Chart.yaml 当前chart属性配置信息
   - templates 编写yaml文件放到该目录中
   - values.yaml yaml可以使用的全局变量
2. 创建配置文件
3. 安装mychart
4. 应用升级
   ```
   helm upgrade xxx mychart

   ```
### 实现yaml高效复用
- 通过传递参数，动态渲染模板，yaml内容动态传入参数生成
- 在chart有values.yaml文件,定义yaml文件爱你全局变量

1. 在alues。yaml定义变量和值
2. 在具体yaml文件爱你，获取定义变量值

value.yaml定义对象
- image
- tag
- label
- port
- replicas

1. 在values.yaml定义全局变量
2. 在templates的yaml使用alues.yaml
   - t通过表达式形式使用全局变量
     - {{ .Values.变量名称}}