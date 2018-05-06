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

create a logstash configuration file in /etc/logstash/conf.d/
```
vim /etc/logstash/conf.d/logstash-mysql.conf
```
and connect to the db:
```
input {
	jdbc {
		jdbc_connection_string => "jdbc:mysql://localhost:3306/movielens"
		jdbc_user => "root"
		jdbc_password => "password"
		jdbc_driver_library => "/home/username/mysql-connector-java-5.1.46/mysql-connector-java-5.1.46-bin.jar"
		jdbc_driver_class => "com.mysql.jdbc.Driver"
		statement => "SELECT * FROM movies"
	}
}

output {
	stdout { codec => json_lines }
	elasticsearch {
		"hosts" => "localhost:9200"
		"index" => "movielens-sql"
		"document_type" => "data" 
	}
}
```
N.B: the jdbc_driver_library => must pint to the jdbc driver that you had downloaded and unzip before...

# import database:
download database from movielens:
```
wget http://files.grouplens.org/datasets/movielens/ml-100k.zip
```

unzip the file:
```
unzip ml-100k.zip
```

open database mysql:
```
mysql -u root -p 
```
create db:
```
CREATE DATABASE movielens;
```
create table:
```
CREATE TABLE movielens.movies (
movieID INT PRIMARY KEY NOT NULL,
title TEXT,
releaseDate DATE
);
```

load data:
```
LOAD DATA LOCAL INFILE 'movielens/ml-100k/u.item' INTO TABLE movielens.movies FIELDS TERMINATED BY '|'
(movieID, title, @var3)
set releaseDate = STR_TO_DATE(@var3, '%d-%M-%Y');
```

check with a simple query if the data are imported:
```
USE movielens
```

```
SELECT * FROM movies WHERE title LIKE 'Start%';
```

```
mysql> SELECT * FROM movies WHERE title LIKE 'Star%';
+---------+------------------------------------------------+-------------+
| movieID | title                                          | releaseDate |
+---------+------------------------------------------------+-------------+
|      50 | Star Wars (1977)                               | 1977-01-01  |
|      62 | Stargate (1994)                                | 1994-01-01  |
|     222 | Star Trek: First Contact (1996)                | 1996-11-22  |
|     227 | Star Trek VI: The Undiscovered Country (1991)  | 1991-01-01  |
|     228 | Star Trek: The Wrath of Khan (1982)            | 1982-01-01  |
|     229 | Star Trek III: The Search for Spock (1984)     | 1984-01-01  |
|     230 | Star Trek IV: The Voyage Home (1986)           | 1986-01-01  |
|     271 | Starship Troopers (1997)                       | 1997-01-01  |
|     380 | Star Trek: Generations (1994)                  | 1994-01-01  |
|     449 | Star Trek: The Motion Picture (1979)           | 1979-01-01  |
|     450 | Star Trek V: The Final Frontier (1989)         | 1989-01-01  |
|    1068 | Star Maker, The (Uomo delle stelle, L') (1995) | 1996-03-01  |
|    1265 | Star Maps (1997)                               | 1997-01-01  |
|    1293 | Star Kid (1997)                                | 1998-01-16  |
|    1464 | Stars Fell on Henrietta, The (1995)            | 1995-01-01  |
+---------+------------------------------------------------+-------------+
15 rows in set (0,00 sec)

```

Start logstash:
```
cd /usr/share/logstash/
```

```
sudo bin/logstash -f /etc/logstash/conf.d/logstash-mysql.conf
```

# check in elasticsearch:
```
curl -H "Content-Type: application/json" -XGET  '127.0.0.1:9200/movielens-sql/_search?q=Star&pretty'
```
http://127.0.0.1:9200/movielens-sql/_search?q=Star&pretty
