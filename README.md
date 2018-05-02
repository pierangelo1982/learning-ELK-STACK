### ELK STACK

# install Java
```
sudo apt-get install default-jdk
```

# Elasticsearch 6 
```
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```

```
sudo apt-get install apt-transport-https
```

```
echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list
```

```
sudo apt-get update && sudo apt-get install elasticsearch
```

edit the elasticsearch.yml file:

```
sudo vi /etc/elasticsearch/elasticsearch.yml
```

Change network.host to 0.0.0.0 

```
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable elasticsearch.service
sudo /bin/systemctl start elasticsearch.service
```

so, now Elasticsearch should be running!

