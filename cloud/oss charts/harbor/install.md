## harbor 部署
创建pv-pvc.yaml


执行
```shell
mdkir -p /app/k3s-install/charts
cd /app/k3s-install/charts
# 下载harbor helm chart
helm add r
helm pull
tar -zxf harbor-1.13.1.tgz
cd harbor
vi pv-pvc.yaml
kubectl create ns system-harboor
kubectl apply -f pv-pvc.yaml
vi custom_values.yaml
kubectl create secret tls demo-tls-secret --cert=/etc/nginx/ssl/_.demo.com_bundle.crt --key=/etc/nginx/ssl/demo.com.key -n system-harbor
helm upgrade  --install caas-harbor-uat . -n system-harbor --values custom-values.yaml
```