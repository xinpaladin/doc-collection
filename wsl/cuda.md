### windows11开启NVIDIA硬件加速
为了docker|jellyfin

参考文档：
https://www.bilibili.com/opus/1023305374538661913
https://learn.microsoft.com/en-us/windows/ai/directml/gpu-cuda-in-wsl
https://docs.nvidia.com/cuda/wsl-user-guide/index.html#getting-started-with-cuda-on-wsl-2
https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html

#### 在wsl2中安装cuda
[官方文档](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&Distribution=WSL-Ubuntu&target_version=2.0)

```shell
# 需要外网
wget https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-9
```


#### 安装NVIDIA Container Toolkit
[官方文档](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html)

1. 配置生产`apt`仓库
```shell
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
  && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```
2. 安装
```shell
# update需要外网， install不需要外网...奇怪
sudo apt update  
sudo apt-get install -y nvidia-container-toolkit
```