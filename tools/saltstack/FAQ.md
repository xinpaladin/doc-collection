1. ext_pillar使用过程中, 使用`sudo systemctl start salt-master` 无法获取 存储在mongo中的数据， 使用`sudo salt-master `可以获取mongo的数据
2. 配置ldap eauth ,配置中携带中文解析错误，导致服务无法启动
3. minion节点需要python3环境
4. saltstack pkg.install python2 和python3

```
install_python:
  pkg.installed:
    - name: python3-pip
    - reload_modules: True

install_inotify:
  pip.installed:
    - name: pyinotify
    - bin_env: /usr/bin/python3.5
```
5. beacons, ubuntu 系统使用systemctl启动无法，文件改动的监控无法发送event到master， 使用
```
Salt Version:
        Salt: 3002.2
 
Dependency Versions:
          cffi: 1.14.4
      cherrypy: Not Installed
      dateutil: 2.4.2
     docker-py: Not Installed
         gitdb: Not Installed
     gitpython: Not Installed
        Jinja2: 2.8
       libgit2: Not Installed
      M2Crypto: Not Installed
          Mako: Not Installed
       msgpack: 0.6.2
  msgpack-pure: Not Installed
  mysql-python: Not Installed
     pycparser: 2.20
      pycrypto: Not Installed
  pycryptodome: 3.4.7
        pygit2: Not Installed
        Python: 3.5.2 (default, Oct  7 2020, 17:19:02)
  python-gnupg: 0.3.8
        PyYAML: 3.11
         PyZMQ: 17.1.2
         smmap: Not Installed
       timelib: Not Installed
       Tornado: 4.5.3
           ZMQ: 4.1.4
 
System Versions:
          dist: ubuntu 16.04 Xenial Xerus
        locale: UTF-8
       machine: x86_64
       release: 4.4.0-173-generic
        system: Linux
       version: Ubuntu 16.04 Xenial Xerus

```