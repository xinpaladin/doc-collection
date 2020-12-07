# Grains

Grains 收集操作系统，域名，IP地址，内核，操作系统类型，内存和许多其他系统属性。

grains 可用于Salt模块和组件，以便正确的Salt Minion命令在正确的系统上自动可用。

grain数据是相对静态的，如果系统信息发生更改（例如，如果网络设置已更改），或者如果将新值分配给自定义 grain，则grain 数据也会刷新。

## 自定义数据
1. minion 添加 `/etc/salt/grains` 文件
```
virtual: openstack
```
2. 添加内容到`/etc/salt/minion`
3. master节点