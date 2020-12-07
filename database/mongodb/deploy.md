### 一、安装Mongodb

#### 步骤
1. 创建`/etc/yum.repos.d/mongodb-org-4.4.repo`
```
[mongodb-org-4.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.4.asc
```
2. 安装

```
sudo yum install -y mongodb-org
```

#### 安装问题
1. 启动错误
    ```
    "msg":"Failed to unlink socket file","attr":{"path":"/tmp/mongodb-27017.sock","error":"Operation not permitted"}}
    ```

    原因分析：

    查看该文件情况如下：


    可以看到这个文件的所有者是root用户，因为在准备环境的时候用的是root用户，而当时用root用户启动过一个没成功，后面修改配置文件过后用mongodb用户启动就出现了这个问题。

    解决方法：

    直接把该文件删除掉用Mongodb用户启动就行了。

    可以看到这一次就是mongodb用户创建的文件了