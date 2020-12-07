### deb
```
curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.10.0-amd64.deb
sudo dpkg -i elasticsearch-7.10.0-amd64.deb
sudo /etc/init.d/elasticsearch start
```

### rpm

```
curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.10.0-x86_64.rpm
sudo rpm -i elasticsearch-7.10.0-x86_64.rpm
sudo service elasticsearch start
```

### mac

```
curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.10.0-darwin-x86_64.tar.gz
tar -xzvf elasticsearch-7.10.0-darwin-x86_64.tar.gz
cd elasticsearch-7.10.0
./bin/elasticsearch

```