# Salt 配置
配置文件在`/etc/salt`目录下， `master`和`minion`文件  
`minion`节点需要配置`master`参数

## [MASTER 配置](https://docs.saltstack.com/en/latest/ref/configuration/master.html#configuration-salt-master)


## [MINION 配置](https://docs.saltstack.com/en/latest/ref/configuration/minion.html#configuration-salt-minion)



## [PROXY MINION 配置](https://docs.saltstack.com/en/latest/ref/configuration/proxy.html#configuration-salt-proxy)


## 启动SALT

```bash
# 测试需要root权限
systemctl start salt-master
systemctl start salt-minion
```
### [使用 `non-root` 用户启动](https://docs.saltstack.com/en/latest/ref/configuration/nonroot.html#configuration-non-root-user)
修改`master`配置文件中的`user`参数，重启`salt-master`  
`minion`同样有`user`参数但以非特权用户身份运行`minion`将使其无法对用户，已安装的软件包等进行更改，除非在minion上设置了访问控制（sudo等）以允许所需要的更改

为了允许Salt成功以非root用户运行，需要设置以下目录的属主和权限以至于可以读写（包括子目录）：
- /etc/salt
- /var/cache/salt
- /var/log/salt
- /var/run/salt

```bash
chown -R user /etc/salt /var/cache/salt /var/log/salt /var/run/salt
```

```bash
# 前台启动
salt-minion 
salt-naster 
# 非root后台启动
salt-minion -d
salt-naster -d
```

> master/minion 配置文件中 `root_dir` `pki_dir` `cachedir` `log_file` 和其他配置,会影响以上的路径

### KEY IDENTITY
salt 提供命令在交换初始化的key之前，验证master和minion的身份。验证密钥标识有助于避免无意中连接到错误的 Salt master节点，并有助于防止在建立初始连接时出现潜在的 MiTM 攻击。

#### MASTER KEY FINGERPRINT
```bash
$> salt-key -F master
Local Keys:
master.pem:  f0:98:15:21:fa:ab:87:61:e4:e5:94:a7:4b:e6:01:69:0f:cd:98:1a:5a:d6:4c:22:35:c6:ff:e0:a1:16:6a:2e
master.pub:  9f:00:92:5a:1c:52:34:22:7c:41:52:a2:37:87:84:4b:36:c8:60:11:3d:18:db:c2:4b:57:65:85:9c:70:ab:71
Accepted Keys:
minion1:  76:6c:a0:2b:03:61:f6:d4:ad:4a:30:47:c4:84:8e:88:fa:18:f5:06:57:b7:46:27:17:fa:62:87:69:a6:7a:50
minion2:  52:3c:44:43:72:cf:b3:00:a6:c9:c6:26:ae:80:61:54:48:87:5c:26:48:a8:ee:5c:5a:af:e4:b9:a3:87:01:56
minion3:  66:da:e3:01:b1:05:1a:c3:36:f8:05:d8:f5:10:46:d3:80:ce:c7:c8:aa:35:03:cf:a9:b2:1d:a3:c1:c6:7e:b0
```
#### MINION KEY FINGERPRINT

```
$> salt-call --local key.finger
local:
    52:3c:44:43:72:cf:b3:00:a6:c9:c6:26:ae:80:61:54:48:87:5c:26:48:a8:ee:5c:5a:af:e4:b9:a3:87:01:56
```

### [KEY 管理](https://docs.saltstack.com/en/latest/ref/cli/salt-key.html#salt-key)
Salt使用AES加密master和minion之间的所有交互。这样可以确保发送到Minions的命令不会被篡改，并且Master和Minion之间的通信是通过受信任的，可接受的密钥进行身份验证的。
```
# master 列出所有的key
$> salt-key -L
```