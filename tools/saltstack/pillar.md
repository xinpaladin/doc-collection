# PILLAR
定义一些属性，使的 salt states 可复用  
master 节点
salt pillar 数据是安全的，可以用来保存 salt states 的部分密码

### SALT PILLAR TOP FILE
salt pillar 使用 Top file 匹配 Salt pillar  data to Salt minions

路径： /srv/pillar/  
top.sls
```yaml
base:
  '*':
    - default
```
default.sls
```
editor: vim
```

更新salt pillar 值后，需要在所有minions刷新pillar值
```bash
salt '*' saltutil.refresh_pillar
```

### 在state配置文件中使用pillar的值
```yaml
vim installed:
  pkg.installed:
    - name: {{ pillar['editor'] }}
```

### 命令行中使用 SALT PILLAR
```bash
salt '*' state.apply ftpsync pillar='{"ftpusername": "test", "ftppassword": "0ydyfww3giq8"}'
```

### SALT PILLAR 几乎能自定义SALT STATE 的任意部分 


## 使用JINJA