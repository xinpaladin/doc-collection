## 安装NodeJS

### 环境预安装

```shell
yum -y update
yum -y install epel-release
```

```shell
yum -y groupinstall "Development Tools"
yum -y install git ImageMagick npm
```

### 通过nvm安装nodejs
```
curl https://raw.githubusercontent.com/creationix/nvm/v0.13.1/install.sh | bash
source ~/.bash_profile
nvm list-remote
nvm install v0.12.7 # as of this writing check the result of the list-remote to see all choices
```

### 源码安装

```
cd /path-to-node/
xz -d node-v10.16.0-linux-x64.tar.xz
$ tar -xvf node-v10.16.0-linux-x64.tar
$ sudo ln -s /path-to-node/node-v10.16.0-linux-x64/bin/node /usr/bin/node
$ sudo ln -s /path-to-node//node-v10.16.0-linux-x64/bin/npm /usr/bin/npm
```