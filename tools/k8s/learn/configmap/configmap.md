# ConfigMap
> 作用： 存储不加密数据到etcd
> 场景： 配置文件

1. 创建配置文件
2. 创建ConfigMap
   ```
    apiVersion: v1
    kind: ConfigMap
    metadata:
    name: myconfig
    namespace: default
    data:
    special.level: info
    special.type: hello

   ```
3. 以Volumn挂载
4. 以var挂载