# Secret
> 作用：加密数据存在etcd中，让Pod容器以挂在Volumn方式进行访问
> 场景：凭证/密码

1. 创建secret加密
  ```yaml
  apiVersion: v1
  king: Secret
  metadata:
    name: mysecret
  type: Opaque
  data:
    username: emhhbmd4MDE2Cg==
    password: YWFhYWE4ODgK
  ```
2. 以vol形式挂载
   ```yaml
   - name: SECRET_USERNAME
     valueFrom:
       secretKeyRef:
         name: mysecret
         key: username
   - name: SECRET_PASSWORD
     valueFrom:
       secretKeyRef:
         name: mysecret
         key: password
   ```
   ```shell
   # 容器内部
   echo $SECRET_USERNAME
   echo $SECRET_PASSWORD
   ```
3. 以数据卷形式挂载
   ```yaml
   volumes:
   - name: foo
     secret:
       secretName: mysecret
   ```

   ```shell
   # 进入容器内部
   cat /etc/foo/password
   cat /etc/foo/username
   ```