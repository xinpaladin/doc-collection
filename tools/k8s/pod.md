apiVersion: v1    　　　　　　# 必选，版本号
kind: Pod         　　　　　　# 必选，Pod
metadata:         　　　　　　# 必选，元数据
  name: String    　　　　　　# 必选，Pod名称
  namespace: String    　　　 # 必选，Pod所属的命名空间
  labels:            　　　　 # 自定义标签，Map格式
    Key: Value    　　　　　　# 键值对
  annotations:    　　　　　　# 自定义注解
    Key: Value    　　　　　  # 键值对
spec:            　　　　　　 # 必选，Pod中容器的详细属性
  containers:        　　　　 # 必选，Pod中容器列表
  - name: String   　　　　   # 必选，容器名称
    image: String    　　　　 # 必选，容器的镜像地址和名称
    imagePullPolicy: {Always | Never | IfNotPresent}    # 获取镜像的策略，Always表示下载镜像，IfnotPresent 表示优先使用本地镜像，否则下载镜像，Never表示仅使用本地镜像。
    command: [String]         # 容器的启动命令列表(覆盖)，如不指定，使用打包镜像时的启动命令。
    args: [String]            # 容器的启动命令参数列表
    workingDir: String        # 容器的工作目录
    volumeMounts:             # 挂载到容器内部的存储卷配置
    - name: String            # 引用Pod定义的共享存储卷的名，需用Pod.spec.volumes[]部分定义的卷名
      mountPath: String       # 存储卷在容器内Mount的绝对路径，应少于512字符
      readOnly: boolean       # 是否为只读模式
  ports:                      # 容器需要暴露的端口库号列表
  - name: String              # 端口号名称
    containerPort: Int        # 容器需要监听的端口号
    hostPort: Int             # 可选，容器所在主机需要监听的端口号，默认与Container相同
  env:                        # 容器运行前需设置的环境变量列表
  - name: String              # 环境变量名称
    value: String             # 环境变量的值
  resources:                  # 资源限制和请求的设置
    limits:                   # 资源限制的设置
      cpu: String             # Cpu的限制，单位为Core数，将用于docker run --cpu-shares参数，如果整数后跟m，表示占用权重，1Core=1000m
      memory: String          # 内存限制，单位可以为Mib/Gib，将用于docker run --memory参数
    requests:                 # 资源请求的设置
      cpu: string             # CPU请求，容器启动的初始可用数量
      memory: string          # 内存请求，容器启动的初始可用数量
  livenessProbe:    # 对Pod内容器健康检查设置，当探测无响应几次后将自动重启该容器，检查方法有exec、httpGet和tcpSocket，对一个容器只需设置其中一种方法即可。
    exec:                     # 对Pod容器内检查方式设置为exec方式
      command: [String]       # exec方式需要制定的命令或脚本
    httpGet:                  # 对Pod容器内检查方式设置为HttpGet方式，需要指定path、port
      path: String            # 网址URL路径（去除对应的域名或IP地址的部分）
      port: Int               # 对应端口
      host: String            # 域名或IP地址
      schema: String          # 对应的检测协议，如http
      HttpHeaders:            # 指定报文头部信息
      - name: String
        value: String
    tcpSocket:                # 对Pod容器内检查方式设置为tcpSocket方式
      port: Int
    initialDelaySeconds: Int  # 容器启动完成后首次探测的时间，单位为秒
    timeoutSeconds: Int       # 对容器健康检查探测等待响应的超时时间，单位为秒，默认为1秒
    periodSeconds: Int        # 对容器监控检查的定期探测时间设置，单位为秒，默认10秒一次
    successThreshold: Int     # 探测几次成功后认为成功
    failureThreshold: Int     # 探测几次失败后认为失败
    securityContext:
      privileged: false
  restartPolicy: {Always | Never | OnFailure}  # Pod的重启策略，Always表示一旦不管以何种方式终止运行，kubelet都将重启，OnFailure表示只有Pod以非0退出码才重启，Nerver表示不再重启该Pod
  nodeSelector:              # 设置NodeSelector表示将该Pod调度到包含这个label的node上，以Key:Value的格式指定
    Key: Value               # 调度到指定的标签Node上
  imagePullSecrets:          # Pull镜像时使用的secret名称，以Key:SecretKey格式指定
  - name: String
  hostNetwork: false         # 是否使用主机网络模式，默认为false，如果设置为true，表示使用宿主机网络
  volumes:                   # 在该Pod上定义共享存储卷列表
  - name: String             # 共享存储卷名称（Volumes类型有多种）
    emptyDir: { }            # 类型为emptyDir的存储卷，与Pod同生命周期的一个临时目录，为空值
    hostPath: String         # 类型为hostPath的存储卷，表示挂载Pod所在宿主机的目录
    path: String             # Pod所在宿主机的目录，将被用于同期中Mount的目录
  secret:                    # 类型为Secret的存储卷，挂载集群与定义的Secret对象到容器内部
    secretname: String
    items:                   # 当仅需挂载一个Secret对象中的指定Key时使用
    - key: String
     path: String
  configMap:                 # 类型为ConfigMap的存储卷，挂载预定义的ConfigMap对象到容器内部
    name: String
    items:                   # 当仅需挂载一个ConfigMap对象中的指定Key时使用
    - key: String
      path: String