# MANAGE FILES

Salt 内置的文件服务能够传输文件和文件夹来管理系统
### SALT://

可以使用 salt://+相对于srv/salt的相对路径，在salt states引用这些文件爱你

### FILE.MANAGED
Salt state function 允许你通过在master上指定source 文件管理本地文件爱你
```yaml
deploy the http.conf file:
  file.managed:
    - name: /etc/http/conf/http.conf
    - source: salt://apache/http.conf
```
Since the source path starts with salt://, we can determine that the source for this file is /srv/salt/apache/http.conf on our Salt master.

Each time this Salt state is applied, Salt makes sure that the local file matches the version on the server. This helps prevent configuration drift and helps make sure that applications are configured identically on different systems.

For example, if you want to distribute a global configuration file for lftp that includes a custom setting that limits download rates, we can do this using file.managed:
```yaml
install network packages:
  pkg.installed:
    - pkgs:
      - rsync
      - lftp
      - curl

copy lftp config file:
  file.managed:
    - name: /etc/lftp.conf
    - source: salt://_tmpl_lftp.conf
```
Another option, since our configuration change is a single line, is to use file.append to simply insert the new line we want to add:
```yaml
install network packages:
  pkg.installed:
    - pkgs:
      - rsync
      - lftp
      - curl

update lftp conf:
  file.append:
    - name: /etc/lftp.conf
    - text: set net:limit-rate 100000:500000
```
### FILE.RECURSE
This Salt state function copies an entire directory.
```yaml
copy some files to the web server:
  file.recurse:
    - name: /var/www
    - source: salt://apache/www
```
### SUMMARY
[SaltStack Formula Repo](https://github.com/saltstack-formulas) 有很多例子.

### 文件模板
Salt使您可以使用模板和变量来管理文件。 看一下[salt.states.file](https://docs.saltstack.com/en/latest/ref/states/all/salt.states.file.html)文档以了解更多信息。

GIT文件服务器
许多组织将所有配置和资源文件存储在源代码控制下以进行更改跟踪。 Salt使您可以使用Git存储库作为文件服务器，而不是（或除了）Salt主服务器上的本地文件系统之外，还可以大大简化此工作流程。

例如，如果您的http.conf来自Git存储库，则每次提交更改后，所有目标系统都将在下次运行file.managed Salt状态时收到更改。

请参阅[Git文件服务器后端演练](https://docs.saltstack.com/en/latest/topics/tutorials/gitfs.html#git-fileserver-backend-walkthrough)以了解如何将自己的Git存储库设置为Salt文件服务器后端。