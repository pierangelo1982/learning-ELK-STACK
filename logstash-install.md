## install logstash
```
sudo apt-get update
```
```
sudo apt-get install logstash
```

# create conf file
in /etc/logstash/conf.d/nomefile.conf
```
/etc/logstash/conf.d/logstash.conf
```

file logstash.conf
```
input {
	file {
		path => "/home/elk/access_log"
		start_position => "beginning"
		ignore_older => 0
	}
}

filter {
	grok {
		match => { "message" => "%{COMBINEDAPACHELOG}" }
	}
	date {
		match => [ "timestamp", "dd/MMM/yyyy:HH:mm:ss Z" ]
	}
}

output {
	elasticsearch {
		hosts => ["localhost:9200"]
	}
	stdout {
		codec => rubydebug
	}
}

```
go in:
```
cd /usr/share/logstash/
```
start:
```
sudo bin/logstash -f /etc/logstash/conf.d/logstash.conf
```