# ELK STACK

### install Java
```
sudo apt-get install default-jdk
```

### Elasticsearch 6 
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

Check if it running at this address: http://127.0.0.1:9200/
```
http://127.0.0.1:9200/
```

At this address you should see something as this:
```
{
  "name" : "ktJu_0M",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "-zyJ1svGQu-nU5iKa23Z2w",
  "version" : {
    "number" : "6.2.4",
    "build_hash" : "ccec39f",
    "build_date" : "2018-04-12T20:37:28.497551Z",
    "build_snapshot" : false,
    "lucene_version" : "7.2.1",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
```

