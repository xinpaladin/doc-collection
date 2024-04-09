
参考文档：

[Kubernetes Operator for OpenTelemetry Collector (github.com)](https://github.com/open-telemetry/opentelemetry-operator#getting-started)  
[opentelemetry-helm-charts (github.com)](https://github.com/open-telemetry/opentelemetry-helm-charts/tree/main/charts/opentelemetry-operator)   
[cert-manager](https://cert-manager.io/)  


Opentelemetry Collector
```
kubectl apply -f otel-collector-deployment.yaml -n opentelemetry
# 给serviceaccount 加入imagePullSecrets， 后删除pod
kubectl patch serviceaccount otel-collector-collector -n opentelemetry -p '{"imagePullSecrets": [{"name": "default-secret"}]}'


kubectl apply -f instrument-deployment.yaml -n opentelemetry
```