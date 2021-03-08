# 命令 Demo

## key 相关

```bash
# master
salt-key -L

# minion
```

## 
```bash
salt '*' cmd.run 'ls -l /etc'
salt '*' disk.usage
salt '*' pkg.install cowsay
salt '*' network.interfaces
```

## 自定义执行节点
```bash
salt 'minion1' disk.usage
salt 'minion*' disk.usage
# 操作系统
salt -G 'os:Ubuntu' test.ping
# 正则
salt -E 'minion[0-9]' test.ping
# minion列表
salt -L 'minion1,minion2' test.ping
# 混合多种
salt -C 'G@os:Ubuntu and minion* or S@192.168.50.*' test.ping
# 控制一次性执行机器数量
salt --batch-size 10 '*' state.apply
```

## 

```bash
#runner实时展示master收到的events
salt-run state.event pretty=True
```


```
sudo salt -a pam '*' test.ping
```