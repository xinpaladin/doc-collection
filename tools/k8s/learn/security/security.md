# k8s集群安全机制

## 概述
1. 访问k8s集群，3个步骤完成具体操作
   1. 认证
   2. 鉴权
   3. 准入控制
2. 进行访问时，过程在都需要经过apiserver，apiserver做统一协调
   - 访问过程中需要证书、token、用户名/密码
   - 访问pod，需要serviceAccount


### 认证

- 传输安全： 对外不暴露8080端口，只能内部访问，对外使用端口6443
- 认证
  - https证书认证，基于ca证书
  - http token人在，通过token识别用户
  - http基本认证，用户名+密码认证

### 鉴权

- 基于rbac模式进行鉴权
- 基于角色访问

### 准入控制
- 准入控制器的列表，如果列表有请求内容，通过；没有拒绝


## RBAC
基于角色的访问控制
- 角色
  - role： 特点命名空间访问权限
  - ClusterRole: 所有命名空间访问权限
- 角色绑定
  - roleBingding: 角色绑定到主体
  - ClusterRoleBinding： 集群角色绑定到主体
  - serviceAccount： 服务账号