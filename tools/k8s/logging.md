# rancher-logging

elastic              	http://10.60.97.145:8080/repository/elastic/                          
ci-banzaicloud-stable	http://10.60.97.145:8080/repository/banzaicloud-stable/ 

helm upgrade --install --wait --namespace logging logging-operator-logging ci-banzaicloud-stable/logging-operator-logging   --set createCustomResource=false --set fluentd.image.repository=10.60.97.145:8082/banzaicloud/fluentd --set fluentd.fluentdPvcSpec.storageClassName=""
