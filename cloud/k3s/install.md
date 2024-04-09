# k3s集群搭建
> 预期目标：
> 1. 创建k3s集群，(3个节点，)
> 2. 集群部署rancher harbor

## 准备工作
1. 创建 `/app/k3s-install` 目录
2. 根据k3s官方文档下载`k3s`和`k3s-airgap-images-amd64.tar`, 放到`/app/k3s-install/k3s-<version>`目录下, **注意k3s版本选择，要注意选择rancher支持的版本**
3. 下载`install.sh`放到`/app/k3s-install` 目录
4. 创建`deploy.sh`, 放到`/app/k3s-install` 目录
   ```
   function install()
    {
        pkg_dir="${base_path}/k3s-${version}"
        cd "${pkg_dir}"
        chmod +x k3s
        cp ./k3s /usr/local/bin/
        images_dir="/app/k3s/k3s/agent/images"
        mkdir -p "${images_dir}"
        gunzip -d k3s-airgap-images-amd64.tar.gz
        cp k3s-airgap-images-amd64.tar "${images_dir}"
    }

    function uninstall()
    {
        /usr/local/bin/k3s-uninstall.sh
        rm -rf "${k3s_data_dir}"
    }

    function show() 
    { 
        echo "parameter error...";
        echo "Usage 1: sh deploy.sh uninstall";
        echo "Usage 2: sh deploy.sh deploy <VERSION>";   
        exit 1;
    }

    command=$1
    base_path="/app/k3s-install"
    k3s_data_dir="/app/k3s/k3s"
    if [ "${command}" == 'uninstall' ]; then
        stop;
        uninstall;  
    elif [ "${command}" == 'install' ]; then
        version=$2
        install;
    else
        show;
    fi 
   ```
5. 下载rancher和harbor的helm chart以及相关镜像
   
## k3s部署
1. 若部署过k3s，执行`cd /app/k3s-install && sh deplop.sh uninstall`
2. 执行`sh deploy.sh install <verison>`
3. 对于第一个**master**执行
    ```
    INSTALL_K3S_SKIP_DOWNLOAD=true ./install.sh server --data-dir /app/k3s/k3s --write-kubeconfig-mode "0644" --cluster-init
    ```
4. 等第一个节点启动后，读取`/app/k3s/k3s/server/token`内容，其他节点执行
    ```
    K3S_URL=https://10.60.83.64:6443 K3S_TOKEN=K10627f21cc43ab23184fd25e4f754dc64e4e7d2edee4f3bdca5fb6d829d673f577::server:9e05b1144b1bd85376b23eef249ae5f5 INSTALL_K3S_SKIP_DOWNLOAD=true ./install.sh server --data-dir /app/k3s/k3s
    ```
5. 配置k3s镜像仓库, 创建`/etc/rancher/k3s/registries.yaml`
   ```
   mirrors:
     docker.io:
       endpoint:
       - "http://ci-repo.demo.com:8082"
     quay.io:
       endpoint:
       - "http://ci-repo.demo.com:8082"
     caas-harbor-uat.demo.com:
       endpoint:
       - "http://caas-harbor-uat.demo.com:80"
   ```