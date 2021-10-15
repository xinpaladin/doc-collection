kubectl api-resources --verbs=list --namespaced -o name | xargs -n 1 kubectl get --show-kind --ignore-not-found -n elastic-system


kubectl apply -f es.yaml -n cattle-logging-system
kubectl apply -f kibana.yaml -n cattle-logging-system

kubectl apply -f es.yaml -n logging
kubectl apply -f kibana.yaml -n logging

curl -k -H "Content-Type:application/json" -X PUT --data-binary @elas.json http://127.0.0.1:8081/api/v1/namespaces/elastic-system/finalize


kubectl -n logging get secret quickstart-es-elastic-user -o=jsonpath='{.data.elastic}' | base64 --decode; echo
kubectl -n cattle-logging-system get secret quickstart-es-elastic-user -o=jsonpath='{.data.elastic}' | base64 --decode; echo


PASSWORD=$(kubectl get secret quickstart-es-elastic-user -o go-template='{{.data.elastic | base64decode}}')

kubectl -n logging get secret quickstart-es-elastic-user -o go-template='{{.data.elastic | base64decode}}'
RO2k2R4AOq60W2n95B22icQU
curl -u "elastic:RO2k2R4AOq60W2n95B22icQU" -k "https://quickstart-es-http:9200"
curl -u "elastic:RO2k2R4AOq60W2n95B22icQU" -k "https://10.43.167.60:9200"
curl -u "elastic:RO2k2R4AOq60W2n95B22icQU" -k "https://10.43.167.60:9200"
curl -u "elastic:RO2k2R4AOq60W2n95B22icQU" -k "http://10.43.167.60:9200"
curl -u "elastic:5lax81d3vf5u3lgVL47UcI34" -k "http://10.43.97.32:9200"

curl -u "logging-quickstart-kibana-user:5fD3Xm7637k5k5ZiqP7com8l" "http://10.43.167.60:9200"
curl -u "logging-quickstart-kibana-user:5fD3Xm7637k5k5ZiqP7com8l" "http://quickstart-es-http.logging.svc:9200"

curl -u "elastic:RO2k2R4AOq60W2n95B22icQU" http://10.43.131.197:5601/login

kubectl port-forward service/quickstart-kb-http --address 0.0.0.0 5601 -n logging

```
apiVersion: logging.banzaicloud.io/v1beta1
kind: Output
metadata:
  name: es-output
spec:
  elasticsearch:
    host: quickstart-es-http.logging.svc.cluster.local
    port: 9200
    scheme: https
    ssl_verify: false
    ssl_version: TLSv1_2
    user: elastic
    password:
      valueFrom:
        secretKeyRef:
          name: quickstart-es-elastic-user
          key: elastic
    buffer:
      timekey: 1m
      timekey_wait: 30s
      timekey_use_utc: true
```


cat <<EOF | kubectl apply -n logging -f -
apiVersion: elasticsearch.k8s.elastic.co/v1
kind: Elasticsearch
metadata:
  name: quickstart
spec:
  version: 7.12.0
  nodeSets:
  - name: default
    count: 3
    config:
      node.store.allow_mmap: false
EOF



{"type":"log","@timestamp":"2021-04-13T08:54:40+00:00","tags":["error","savedobjects-service"],"pid":7,"message":"Unable to retrieve version information from Elasticsearch nodes."}
{"type":"log","@timestamp":"2021-04-13T08:54:40+00:00","tags":["warning","plugins","licensing"],"pid":7,"message":"License information could not be obtained from Elasticsearch due to [security_exception] unable to authenticate user [logging-quickstart-kibana-user] for REST request [/_xpack?accept_enterprise=true], with { header={ WWW-Authenticate=\"Basic realm=\\\"security\\\" charset=\\\"UTF-8\\\"\" } } :: {\"path\":\"/_xpack?accept_enterprise=true\",\"statusCode\":401,\"response\":\"{\\\"error\\\":{\\\"root_cause\\\":[{\\\"type\\\":\\\"security_exception\\\",\\\"reason\\\":\\\"unable to authenticate user [logging-quickstart-kibana-user] for REST request [/_xpack?accept_enterprise=true]\\\",\\\"header\\\":{\\\"WWW-Authenticate\\\":\\\"Basic realm=\\\\\\\"security\\\\\\\" charset=\\\\\\\"UTF-8\\\\\\\"\\\"}}],\\\"type\\\":\\\"security_exception\\\",\\\"reason\\\":\\\"unable to authenticate user [logging-quickstart-kibana-user] for REST request [/_xpack?accept_enterprise=true]\\\",\\\"header\\\":{\\\"WWW-Authenticate\\\":\\\"Basic realm=\\\\\\\"security\\\\\\\" charset=\\\\\\\"UTF-8\\\\\\\"\\\"}},\\\"status\\\":401}\",\"wwwAuthenticateDirective\":\"Basic realm=\\\"security\\\" charset=\\\"UTF-8\\\"\"} error"}
