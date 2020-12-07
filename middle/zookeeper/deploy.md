## Zookeeper 安装

1. 官网下载 `apache-zookeeper-3.6.2-bin.tar.gz`
2. 配置java环境
3. 解压压缩包
4. 配置文件修改
    ```
    $ cd /path/to/apache-zookeeper-3.6.2-bin/
    $ cp conf/zoo_sample.cfg conf/zoo.cfg
    根据需求修改配置文件
    ```
5. 启动zookeeper
   ``` bin/zkServer.sh start ```