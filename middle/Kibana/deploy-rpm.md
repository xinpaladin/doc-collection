# [安装](https://www.elastic.co/guide/en/kibana/current/rpm.html#rpm-key)

## 系统需求

- SLES 11 和 CentOS 5以上

## RPM 安装

### 导入 Elastic PGP key
```
rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
```

### installing from the RPM repository
在 `/etc/yum.repos.d/` 目录下创建 `kibana.repo`文件，内容如下：
```
[kibana-7.x]
name=Kibana repository for 7.x packages
baseurl=https://artifacts.elastic.co/packages/7.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
```

```
sudo yum install kibana
```

### 启动 Kibana systemd
开机自启配置
```
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable kibana.service
```
启动/停止
```
sudo systemctl start kibana.service
sudo systemctl stop kibana.service
```

### RPM 安装文件目录

|  Type   | Description                              |     Default Location      |  Setting  |
| :-----: | :--------------------------------------- | :-----------------------: | :-------: |
|  home   | Kibana home directory or $KIBANA_HOME    |     /usr/share/kibana     |           |
|   bin   | server和kibaba-plugin 启动脚本           |   /usr/share/kibana/bin   |           |
| config  | Configuration files including kibana.yml |        /etc/kibana        |           |
|  data   | Kibana 和插件数据写的位置                |      /var/lib/kibana      | path.data |
|  logs   | 日志文件路径                             |      /var/log/kibana      | path.logs |
| plugins | 插件文件位置，每个插件一个子目录         | /usr/share/kibana/plugins |           |








































