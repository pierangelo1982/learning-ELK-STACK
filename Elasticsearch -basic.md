### Basic Of Elasticsearch

Create a mapping for the index, in some way is how create an empty database:
```
curl -H "Content-Type: application/json" -XPUT 127.0.0.1:9200/movies -d '
{
	"mappings": {
		"movie": {
			"properties" : {
				"year" : {"type": "date"}
					}
			}
		}
}'

```