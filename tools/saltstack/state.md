# State
使用YAML配置命令集合


```yaml
ID:
  module.function:
    - name: name
    - argument: value
    - argument: 
      - value1
      - value2
```

ID state的描述，唯一
module.function state模块和功能


## INCLUDES
```yaml
include:
  - sls1
  - sls2
  - dir.sls3
```
sls1 和 sls2 是SLS 文件名。不需要添加.sls后缀

### 例子
srv/salt/lftp.sls
```yaml
install lftp:
  pkg.installed:
    - name: lftp
```

srv/salt/dir-sync.sls
```yaml
include:
  - lftp
sync directory using lftp:
  cmd.run:
    - name: lftp -c "open -u {{ pillar['ftpusername'] }},{{ pillar['ftppassword'] }}
           -p 22 sftp://example.com;mirror -c -R /local /remote"
```



