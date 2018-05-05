### Basic Of Elasticsearch

Create a mapping schema for the index, in some way is how create an empty database:
Crea uno schema per poter poi inidicizzare:
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

check:
http://127.0.0.1:9200/movies?pretty

# insert data into index:
```
curl -H "Content-Type: application/json" -XPUT 127.0.0.1:9200/movies/movie/109487 -d '
{
	"genre" : ["IMAX","Sci-Fi"],
	"title" : "Interstellar",
	"year" : 2014
}'

```

check:
http://127.0.0.1:9200/movies/movie/109487?pretty

# import many data:
# json bulk
```
curl -H "Content-Type: application/json" -XPUT 127.0.0.1:9200/_bulk -d '
{"create" : { "_index" : "movies", "_type" : "movie", "_id" : "135569" } }
{"id": "135569", "title" : "Star Trek Beyond", "year":2016 , "genre":["Action", "Adventure", "Sci-Fi"] }
{"create" : { "_index" : "movies", "_type" : "movie", "_id" : "122886" } }
{"id": "122886", "title" : "Star Wars: Episode VII - The Force Awakens", "year":2015 , "genre":["Action", "Adventure", "Fantasy", "Sci-Fi", "IMAX"] }
{"create" : { "_index" : "movies", "_type" : "movie", "_id" : "109487" } }
{"id": "109487", "title" : "Interstellar", "year":2014 , "genre":["Sci-Fi", "IMAX"] }
{"create" : { "_index" : "movies", "_type" : "movie", "_id" : "58559" } }
{"id": "58559", "title" : "Dark Knight, The", "year":2008 , "genre":["Action", "Crime", "Drama", "IMAX"] }
{"create" : { "_index" : "movies", "_type" : "movie", "_id" : "1924" } }
{ "id": "1924", "title" : "Plan 9 from Outer Space", "year":1959 , "genre":["Horror", "Sci-Fi"]}
'

```

# update content
```
curl -H "Content-Type: application/json" -XPOST 127.0.0.1:9200/movies/movie/109487/_update -d '
{
	"doc": {
		"title": "Interstellarooo"
		}
}'
```

# delete
```
curl -H "Content-Type: application/json" -XDELETE 127.0.0.1:9200/movies/movie/58559
```

# vedi tutti
```
http://localhost:9200/movies/_search?pretty=true&q=*:*

curl -H "Content-Type: application/json" -XGET 127.0.0.1:9200/movies/_search?pretty=true&q=*:*
```



