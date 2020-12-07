## Event-Driven Infrastructure
展示内容：
- 监视并启用Salt事件
- 启用对non-Salt活动的监视
- 根据事件触发反应

Event-Driven 使用场景：
- 用户登录生产服务器时发送文本消息。
- 构建失败时通知Slack或HipChat channel。
- 当有未经授权的更改后，立即还原配置文件。
- 自动扩展云资源。
- 监视磁盘，处理器，内存和其他系统统计信息，并在定义的阈值之外采取措施。
## Watching Events
Salt 内部组件交互通过发送和监听events。
- Salt minion 连接
- 密钥(key)被接受或拒绝
- Job is sent
- minion 返回 Job 结果
- 在线状态心跳（默认关闭）
- salt命令行接口
###
## Custom Event

## Beacons
Salt beacons 监控和发送Salt events
- file system changes
- system load
- service status
- shell activity, such as user login
- network and disk usage

### 监听文件修改demo
安装`python-pyinotify`到`minion1`：
```bash
sudo salt 'minion1' pkg.install python-pyinotify
```
有坑：
默认安装在python2的环境，实际环境是python3,导致minion节点无法运行beacon
```
[WARNING ] Unable to process beacon inotify
```

需要自行安装pip3，
```
sudo pip3 install pyinotify
```

minion1， 编辑`/etc/salt/minion`文件，添加以下内容到文件底部：
```yaml
beacons:
  inotify:
    /home/vagrant/importantfile:
      mask:
        - modify
```
保存文件，重启salt minion服务：
```bash
sudo systemctl restart salt-minion
```

## Event Reactor

### Reactions类型

Salt reactors trigger one of the following systems:

- Remote execution(远程执行)：在目标minion上运行一个执行模块.您可以通过调用salt命令（包括应用state or highstate）来完成此操作。
- Salt Runner： 使用salt-run启动的任务。 例如，HTTP runner 可以触发Webhook。
- Wheel：Wheel 命令管理您的Salt环境，执行诸如接受密钥和更新配置之类的任务。 salt-key实用程序公开了其中一些任务。


### REMOTE EXECUTION

```bash
salt 'myminion' pkg.install cowsay
```
reactor SLS,
```yaml
install cowsay on myminion:
  local.pkg.install:
    - tgt: 'myminion'
    - arg:
      - cowsay
```