1. initialization failed for volume 
https://github.com/kubernetes-sigs/sig-storage-local-static-provisioner/issues/107
https://rancher.com/docs/rke/latest/en/config-options/services/services-extras/#extra-binds
解决：
原因是rancher部署的kubelet 是容器检测不到宿主机目录
kubeconfig 添加 extra_binds
```
services:
    kubelet:
      extra_binds:
        - "/host/dev:/dev"
        - "/usr/libexec/kubernetes/kubelet-plugins:/usr/libexec/kubernetes/kubelet-plugins:z"
```
