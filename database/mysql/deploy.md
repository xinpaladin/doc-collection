### 一、安装MySql
1. 第一种方法

    获取rpm
    ```
    shell> wget https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
    ```
    安装MySQL-community-server
    ```
    shell> sudo yum install mysql-community-server
    ```
2. 第二种

    下载RPM bundle tar file 
    ```
    mysql-5.7.19-1.el6.x86_64.rpm-bundle.tar
    ```
    解压
    ```
    shell> tar -xf mysql-5.7.24-1.el7.x86_64.rpm-bundle.tar
    ```
    安装
    ```
    shell> sudo yum install -y mysql-community-{server,client,common,libs,devel}-* --exclude='*minimal*

    ```
    根据环境，选择以上任一方法安装mysql，==内网环境==请使用第二种

    启动mysql服务
    ```
    shell> sudo systemctl start mysqld
    ```
    检查mysql服务
    ```
    shell> sudo systemctl status mysqld
    ```
    修改mysql密码
    ```
    shell> sudo grep 'temporary password' /var/log/mysqld.log
    shell> mysql -uroot -p
    mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';
    ```



