# 常用操作

## 创建数据库

```
CREATE DATABASE dbname;
```

## 创建用户



## 授权



SELECT d.dest_name, DECODE (d.status, 'VALID',3, 'DEFERRED', 2, 'ERROR', 1, 0) AS status, d.log_sequence, d.error FROM v_$archive_dest d , v_$database db WHERE d.status != 'INACTIVE' AND db.log_mode = 'ARCHIVELOG';