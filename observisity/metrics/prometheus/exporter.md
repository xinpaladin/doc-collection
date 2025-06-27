# prometheus 用到的exporter


## blackbox_exporter
blackbox_exporter 是外部探测工具，不依赖目标服务的本地环境，通过模拟客户端请求（如 HTTP GET、TCP 握手）来验证服务是否可达。

## node_exporter
node_exporter 是主机内省工具，依赖目标主机的本地环境（如操作系统接口、文件系统），只能采集主机自身的运行状态，无法感知外部服务的可用性