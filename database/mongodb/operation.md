# 数据库操作

## 查看所有数据库
```
> show dbs
```

## 创建数据库

```
> user xxxx
```
## 用户操作

### 查看用户集合
```
> db.system.users.find()
```

### 用户认证
```
> db.auth('user','password')
```
### 新建用户

For MongoDB 2.6.x and 3.2.x
```
> db.createUser( { user: "nodebb", pwd: "<Enter in a secure password>", roles: [ "readWrite" ] } )
```
For earlier versions of MongoDB (if the above throws an error)
```
> db.addUser( { user: "nodebb", pwd: "<Enter in a secure password>", roles: [ "readWrite" ] } )
```