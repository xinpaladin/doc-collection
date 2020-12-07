# 安装SALT
> git地址: https://github.com/saltstack  
> 参考地址： https://docs.saltstack.com/en/latest/topics/installation/index.html
## Ubuntu 


1. 选择版本执行命令，导入SaltStack repository key
```bash
# 20.04
wget -O - https://repo.saltstack.com/py3/ubuntu/20.04/amd64/latest/SALTSTACK-GPG-KEY.pub | sudo apt-key add -
# 18.04
wget -O - https://repo.saltstack.com/py3/ubuntu/18.04/amd64/latest/SALTSTACK-GPG-KEY.pub | sudo apt-key add -
# 16.04
wget -O - https://repo.saltstack.com/py3/ubuntu/16.04/amd64/latest/SALTSTACK-GPG-KEY.pub | sudo apt-key add -
```
2. 选择版本保存以下内容到`/etc/apt/sources.list.d/saltstack.list`
```bash
# 20.04
deb http://repo.saltstack.com/py3/ubuntu/20.04/amd64/latest focal main
# 18.04
deb http://repo.saltstack.com/py3/ubuntu/18.04/amd64/latest bionic main
# 16.04
deb http://repo.saltstack.com/py3/ubuntu/16.04/amd64/latest xenial main
```
3. 执行`sudo apt-get update`

4. 安装
```bash
apt-get install salt-api
apt-get install salt-cloud
apt-get install salt-master
apt-get install salt-minion
apt-get install salt-ssh
apt-get install salt-syndic
```


### 使用

#### master

```
salt-key --accept-all

```


#### minion
修改 `/etc/salt/minion`
```
master: masterIP
```


## RHEL / CENTOS / SCIENTIFIC LINUX / AMAZON LINUX / ORACLE LINUX

1. 导入 SaltStack 
```
# Version 7
rpm --import https://repo.saltstack.com/yum/redhat/7/x86_64/latest/SALTSTACK-GPG-KEY.pub
```

2. 保存yum仓库
```
[saltstack-repo]
name=SaltStack repo for RHEL/CentOS $releasever
baseurl=https://repo.saltstack.com/yum/redhat/$releasever/$basearch/latest
enabled=1
gpgcheck=1
gpgkey=https://repo.saltstack.com/yum/redhat/$releasever/$basearch/latest/SALTSTACK-GPG-KEY.pub
```