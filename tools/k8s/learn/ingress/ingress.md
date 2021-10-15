# ingress

1. 把端口号对外暴露，通过ip+端口号进行访问
   - 使用Service NodePort
2. NodePort缺陷 每个节点上都会起端口，在访问的时候通过任意节点
   - 每个端口只能使用一次，一个端口对于一个应用
   - 实际访问中都是使用域名，通过不同域名跳转到不同端口服务中

3. Ingress
   - pod和ingress通过service关联
     - ingress作为统一入口，由service关联一组+pod

4. ingress工作流程
5. 使用ingress
   1. 部署ingress Controller
   2. 创建ingress规则

6. 使用ingress对外暴露应用
   1. nodeport 对外暴露端口
   2. 部署ingress controller
   3. 创建ingress规则
   4. 修改hosts，修改域名解析（测试使用）