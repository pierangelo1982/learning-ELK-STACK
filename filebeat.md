### filebeat

# install filebeat

```
sudo apt-get update && sudo apt-get install filebeat
```

# install some useful plugin in elasticsearch
go in /usr/share/elasticsearch/
```
cd /usr/share/elasticsearch/
```

and install:
```
sudo bin/elasticsearch-plugin install ingest-geoip

sudo bin/elasticsearch-plugin install ingest-user-agent
```

restart elasticsearch
```
sudo /bin/systemctl stop elasticsearch.service

sudo /bin/systemctl start elasticsearch.service
```

# setup filebeat dashborad:
go in the filebeat folder:
```
cd /usr/share/filebeat/bin
```

and launch setup command:
```
sudo filebeat setup --dashboards
```

# edit yml file
rename apache2.yml.disabled as apache2.yml
```
sudo mv /etc/filebeat/modules.d/apache2.yml.disabled /etc/filebeat/modules.d/apache2.yml

```
and edit:
```
sudo vi /etc/filebeat/modules.d/apache2.yml
```

and insert the path to you logs files:
```
- module: apache2
  # Access logs
  access:
    enabled: true

    # Set custom paths for the log files. If left empty,
    # Filebeat will choose the paths depending on your OS.
    #var.paths:
    var.paths: "/home/<username>/logs/access*"
  # Error logs
  error:
    enabled: true

    # Set custom paths for the log files. If left empty,
    # Filebeat will choose the paths depending on your OS.
    #var.paths:
    var.paths: "/home/<username>/logs/error"

```

start filebeat:
```
sudo /bin/systemctl start filebeat.service
```
