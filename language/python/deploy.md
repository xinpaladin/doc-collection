## 安装Python3
### 环境预安装

有Python, MySQL的依赖，django-compressor的压缩需要的bizp2-devel等

```shell
[root@zanhu ~]# yum -y update
[root@zanhu ~]# yum -y install python-devel zlib-devel mysql-devel libffi-devel bzip2-devel openssl-devel wget gcc
```
### 六条命令一梭哈

一条条来不容易出问题，阿里云的服务器下载`Python-3.7.2.tar.xz`很慢，可以先浏览器下载了再传到服务器上，后面下载`elasticsearch-2.4.6.tar.gz`也是一样。

```shell
[root@zanhu ~]# wget https://www.python.org/ftp/python/3.7.2/Python-3.7.2.tar.xz
[root@zanhu ~]# tar -xvf Python-3.7.2.tar.xz
[root@zanhu ~]# cd Python-3.7.2
[root@zanhu ~]# ./configure --prefix=/usr/local/python3 --enable-optimizations
[root@zanhu ~]# make
[root@zanhu ~]# make install
```

* 注：加上`--enable-optimizations`后make的过程巨慢，但系统在执行Python代码时会有10%-20%的性能提升，参考 [what does --enable-optimizations do while compiling python?](https://stackoverflow.com/questions/41405728/what-does-enable-optimizations-do-while-compiling-python)

### 创建软链接

```shell
[root@zanhu ~]# ln -s /usr/local/python3/bin/python3 /usr/bin/python3
[root@zanhu ~]# ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3
```

### 验证安装结果

```shell
[root@zanhu ~]# python3 -V
Python 3.7.2
[root@zanhu ~]# pip3 -V
pip 18.1 from /usr/local/python3/lib/python3.7/site-packages/pip (python 3.7)
[root@zanhu ~]# whereis python3
python3: /usr/bin/python3 /usr/local/python3
[root@zanhu ~]# whereis pip3
pip3: /usr/bin/pip3
[root@zanhu ~]#
```