## Basic Of Elasticsearch

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

### insert data into index:
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

### import many data:
### json bulk
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

### update content
```
curl -H "Content-Type: application/json" -XPOST 127.0.0.1:9200/movies/movie/109487/_update -d '
{
	"doc": {
		"title": "Interstellarooo"
		}
}'
```

### delete
```
curl -H "Content-Type: application/json" -XDELETE 127.0.0.1:9200/movies/movie/58559
```

### query
see all data
```
http://localhost:9200/movies/_search?pretty=true&q=*:*

curl -H "Content-Type: application/json" -XGET 127.0.0.1:9200/movies/_search?pretty=true&q=*:*
```

### query lite
```
/movies/movie/_search?q=title:star


http://127.0.0.1:9200/movies/movie/_search?q=title:star

curl -H "Content-Type: application/json" -XGET 127.0.0.1:9200/movies/movie/_search?q=title:star

```

### multiple key value query
```
/movies/movie/_search?q=+year:>2010+title:trek

http://127.0.0.1:9200/movies/movie/_search?q=+year:>2010+title:trek

curl -H "Content-Type: application/json" -XGET 127.0.0.1:9200/movies/movie/_search?q=+year:>2010+title:trek
```


spaces etc. need to be URL encoded
```
/movies/movie/_search?q=%2Byear%3A%3E2010+%2Btitle%3Atrek

http://127.0.0.1:9200/movies/movie/_search?q=%2Byear%3A%3E2010+%2Btitle%3Atrek

curl -H "Content-Type: application/json" -XGET 127.0.0.1:9200/movies/movie/_search?q=%2Byear%3A%3E2010+%2Btitle%3Atrek

```

### request body search
```
curl -H "Content-Type: application/json" -XGET 127.0.0.1:9200/movies/movie/_search?pretty -d '
{
	"query": {
		"match": {
			"title": "star"
			}
		}
}'
```
### boolean query with a filter
```
curl -H "Content-Type: application/json" -XGET 127.0.0.1:9200/movies/movie/_search?pretty -d'
{
"query":{
	"bool": {
		"must": {"term": {"title": "trek"}},
		"filter": {"range": {"year": {"gte": 2010}}}
		}
	}
}'

```

### some type of query filter 

term: filter by exact values
```
{“term”: {“year”: 2014}}
```
terms: match if any exact values in a list match
```
{“terms”: {“genre”: [“Sci-Fi”, “Adventure”] } }
```
range: Find numbers or dates in a given range (gt, gte, lt, lte)
```
{“range”: {“year”: {“gte”: 2010}}}``
```
exists: Find documents where a field exists
```
{“exists”: {“field”: “tags”}}
```
missing: Find documents where a field is missing
```
{“missing”: {“field”: “tags”}}
```
bool: Combine filters with Boolean logic (must, must_not, should)
match_all: returns all documents and is the default. Normally used with a filter.
```
{“match_all”: {}}
```
match: searches analyzed results, such as full text search.
```
{“match”: {“title”: “star”}}
```
multi_match: run the same query on multiple fields.
```
{“multi_match”: {“query”: “star”, “fields”: [“title”, “synopsis” ] } }
```
bool: Works like a bool filter, but results are scored by relevance.

queries are wrapped in a “query”: { } block,
filters are wrapped in a “filter”: { } block.
you can combine filters inside queries, or queries inside filters too.

```
curl -H "Content-Type: application/json" -XGET  127.0.0.1:9200/movies/movie/_search?pretty -d'
{
"query":{
	"bool": {
		"must": {"term": {"title": "trek"}},
		"filter": {"range": {"year": {"gte": 2010}}}
			}
		}
}'
```

### phrase matching
with match phrase the words must be ib the right sequence, for example if star wars is wars star not find
```
curl -H "Content-Type: application/json" -XGET 127.0.0.1:9200/movies/movie/_search?pretty -d '
{
	"query": {
		"match_phrase": {
			"title": "star wars"
			}
		}
}'
```

order matter, but with slop you can accept some little margin of incongruence in the error:
```
curl -H "Content-Type: application/json" -XGET 127.0.0.1:9200/movies/movie/_search?pretty -d '
{
	"query": {
		"match_phrase": {
			"title": {"query": "star beyond", "slop": 1}
			}
		}
}'
```

## pagination syntax
```
curl -H "Content-Type: application/json" -XGET '127.0.0.1:9200/movies/movie/_search?size=2&from=2&pretty'
```

```
curl -H "Content-Type: application/json" -XGET 127.0.0.1:9200/movies/movie/_search?pretty -d'
{
	"from": 2,
	"size": 2,
	"query": {"match": {"genre": "Sci-Fi"}}
}'
```

## sorting
```
curl -H "Content-Type: application/json" -XGET '127.0.0.1:9200/movies/movie/_search?sort=year&pretty'
```

## aggregation
```
curl -H "Content-Type: application/json" -XGET '127.0.0.1:9200/ratings/rating/_search?size=0&pretty' -d '
{
	"aggs": {
		"ratings": {
			"terms": {
				"field": "rating"
				}
			}
		}
}'
```

```
curl -H "Content-Type: application/json" -XGET '127.0.0.1:9200/ratings/rating/_search?size=0&pretty' -d '
{
	"query": {
		"match": {
			"rating": 5.0
		}
	},
	"aggs" : {
		"ratings": {
			"terms": {
				"field" : "rating"
			}
		}
	}
}'
```

```
curl -H "Content-Type: application/json" -XGET '127.0.0.1:9200/ratings/rating/_search?size=0&pretty' -d '
{
	"query": {
		"match_phrase": {
			"title": "Star Wars Episode IV"
			}
		},
	"aggs" : {
		"avg_rating": {
			"avg": {
				"field" : "rating"
			}
		}
	}
}'
```

## histograms
itervals etc...
```
curl -H "Content-Type: application/json" -XGET '127.0.0.1:9200/ratings/rating/_search?size=0&pretty' -d '
{
	"aggs" : {
		"whole_ratings": {
			"histogram": {
				"field": "rating",
				"interval": 1.0
			}
		}
	}
}'
```

```
curl -H "Content-Type: application/json" -XGET '127.0.0.1:9200/movies/movie/_search?size=0&pretty' -d '
{
	"aggs" : {
		"release": {
			"histogram": {
				"field": "year",
				"interval": 10
			}
		}
	}
}'
```

# time
```
curl -H "Content-Type: application/json" -XGET '127.0.0.1:9200/logstash-2018.05.07/_search?size=0&pretty' -d '
{
	"aggs" : {
		"timestamp": {
			"date_histogram": {
				"field": "@timestamp",
				"interval": "hour"
			}
		}
	}
}'
```

```
curl -H "Content-Type: application/json" -XGET '127.0.0.1:9200/logstash-2018.05.07/_search?size=0&pretty' -d '
{
	"query" : {
		"match": {
			"agent": "Googlebot"
		}
	},
	"aggs" : {
		"timestamp": {
			"date_histogram": {
				"field": "@timestamp",
				"interval": "hour"
			}
		}
	}
}'
```

```
curl -H "Content-Type: application/json" -XGET '127.0.0.1:9200/logstash-2018.05.07/_search?size=0&pretty' -d '{
	"query" : {
		"match": {
			"response": "500"
		}
	},
	"aggs" : {
		"timestamp": {
			"date_histogram": {
				"field": "@timestamp",
				"interval": "minute"
			}
		}
	}
}'
```

# Nested
```
curl -H "Content-Type: application/json" -XGET '127.0.0.1:9200/ratings/rating/_search?size=0&pretty' -d '
{
	"query": {
		"match_phrase": {
			"title": "Star Wars"
		}
	},
	"aggs" : {
		"titles": {
			"terms": {
				"field": "title.raw"
			},
		"aggs": {
			"avg_rating": {
				"avg": {
					"field" : "rating"
				}
			}
		}
	}
}
}'
```

# see all index
```
curl -H "Content-Type: application/json" -XGET '127.0.0.1:9200/_cat/indices?v'
```
