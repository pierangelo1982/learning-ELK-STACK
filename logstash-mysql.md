# logstash with mysql

install mysql:
```
sudo apt-get update

sudo apt-get install mysql-server
```

download and install install a jdbc driver

choose Connector/J 8.0.11
and Platform indipendent version:

from here:
https://dev.mysql.com/downloads/connector/j/

```
wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.46.zip
```

unzip mysql-connector:
```
unzip mysql-connector-java-5.1.46.zip
```