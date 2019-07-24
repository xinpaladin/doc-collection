现象:
共享目录是单向的,使用vagrant启动centos/7 ,能够将宿主机当前目录下的文件同步到虚拟机 `/vagrant`,但在虚拟机中对`/vagrant`所做的修改并不会在宿主机目录生效,无法实现双向共享

环境:
- ubuntu 16.04
- virtualBox
- vagrant
- centos/7 box

解决:

vagrant 安装vagrant-vbguest插件,在Vagrantfile中配置synced_folder
- 安装vagrant-vbguest
    ```
    $ vagrant plugin install vagrant-vbguest
    ```
- Vagrantfile中配置共享目录synced_folder
    ```
    config.vm.synced_folder "./share","/vagrant"
    ```