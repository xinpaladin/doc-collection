# 数据库操作

## 创建数据库
```
mysql> CREATE DATABASE `testdb` CHARACTER SET utf8 COLLATE utf8_general_ci;
```
## 创建用户

```
mysql> create user common_user@'localhost' identified by 'password';

```

## 数据库授权

> 简单格式  grant 权限 on 数据库对象 to 用户

- 普通用户的增删改查 数据库所有表的权利
    ```
    mysql> grant select on testdb.* to common_user@'%'
    mysql> grant insert on testdb.* to common_user@'%'
    mysql> grant update on testdb.* to common_user@'%'
    mysql> grant delete on testdb.* to common_user@'%'
    ```
    或者
    ```
    mysql> grant select, insert, update, delete on testdb.* to common_user@'%'
    ```

- 开发人员 创建表、索引、视图、存储过程、函数等权限  
  - grant 创建、修改、删除 MySQL 数据表结构权限。
    ```
    mysql> grant create on testdb.* to developer@'192.168.0.%';
    mysql> grant alter on testdb.* to developer@'192.168.0.%';
    mysql> grant drop on testdb.* to developer@'192.168.0.%';
    ```
  - grant 操作 MySQL 外键权限。
    ```
    mysql> grant references on testdb.* to developer@'192.168.0.%';
    ```
  - grant 操作 MySQL 临时表权限。
    ```
    mysql> grant create temporary tables on testdb.* to developer@'192.168.0.%';
    ```
  - grant 操作 MySQL 索引权限。
    ```
    mysql> grant index on testdb.* to developer@'192.168.0.%';
    ```
  - grant 操作 MySQL 视图、查看视图源代码 权限。
    ```
    mysql> grant create view on testdb.* to developer@'192.168.0.%';
    mysql> grant show view on testdb.* to developer@'192.168.0.%';
    ```
  - grant 操作 MySQL 存储过程、函数 权限。
    ```
    mysql> grant create routine on testdb.* to developer@'192.168.0.%'; -- now, can show procedure status
    mysql> grant alter routine on testdb.* to developer@'192.168.0.%'; -- now, you can drop a procedure
    mysql> grant execute on testdb.* to developer@'192.168.0.%';
    ```
- 普通 DBA 管理某个 MySQL 数据库的权限。
    ```
    mysql> grant all privileges on testdb to dba@'localhost'
    ```
    或 （`privileges` 可省略）
    ```
    mysql> grant all on testdb.* to dba@'localhost';
    
    ```
- 高级 DBA 管理 MySQL 中所有数据库的权限。
  ```
  mysql> GRANT ALL PRIVILEGES ON *.* TO 'dba'@'%' ;
  mysql> GRANT ALL ON *.* TO 'dba'@'%' ;
  ```