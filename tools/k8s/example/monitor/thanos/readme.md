部署命令：
thanos sidecar 
    --tsdb.path                 /qhapp/prometheus/data \
    --objstore.config-file      bucket_config.yaml \       # Bucket config file to send data to
    --prometheus.url            http://localhost:9090 \    # Location of the Prometheus HTTP server
    --http-address              0.0.0.0:19191 \            # HTTP endpoint for collecting metrics on the Sidecar
    --grpc-address              0.0.0.0:19090