### read Amazon S3 log with logstash and index in elasticsearch

Connect to your AWS console and open S3.
xxxx
Create a Bucket and upload a log file...
xxx
Go in AIM and create a user for get api key:
xxx
create a user
xxx
attach a policy to the user, click on "Attach existing policies directly" and in the search field write s3 and select "Amazon Read Only Access"
xxx

go ahead and create the user.
xxxxxx

click on download CSV file (where you will fine your access api key


exit from yout aws console and go on to create the .conf file of logstash:
```
sudo vim /etc/logstash/conf.d/logstash-s3.conf

```

in input section of the .conf file you mast insert the name of tour s3 bucket and the key from the CSV file that you have downloaded
```
input {
	s3 {
		bucket => "pierangelo-logstashtest"
		access_key_id => "AK**********A"
		secret_access_key => "Ic******************ubb"
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

Start Logstash:
```
cd /usr/share/logstash/

sudo bin/logstash -f /etc/logstash/conf.d/logstash-s3.conf
```

check in elasticsearch:

curl -H "Content-Type: application/json" -XGET  '127.0.0.1:9200/logstash-2018.05.07/_search?&pretty'