```
# 已下载rancher helm chart到/qhapp/k3s-install/charts目录下
cd /qhapp/k3s-install/charts
tar -zxf rancher-2.7.9.tgz
cd harbor
kubectl create ns cattle-system
vi custom_values.yaml
kubectl create secret tls tls-rancher-ingress --cert=/etc/nginx/ssl/_.demo.com_bundle.crt --key=/etc/nginx/ssl/demo.com.key -n cattle-system
helm upgrade --install rancher .  --namespace cattle-system -f custom_values.yaml
```