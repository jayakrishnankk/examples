# Getting started with Elasticsearch

## An introduction to Elasticsearch

**Agenda**

 * Prerequisites
 * Download and installation
 * Kibana Installation
 * X-Pack Installation
 * Getting started with Elastic Search

**Main Features of elasticsearch**

 * Horizontal Scale
 * Real-Time Availability
 * Flexible Data Model
 * Rapid Query Execution
 * Sophisticated Query Language
 * Schemaless

## Prerequisites
Elastic search requires Java 8.

**Linux**
Installing oracle java 8

```
sudo add-apt-repository -y ppa:webupd8team/java
sudo apt-get update
sudo apt-get -y install oracle-java8-installer
java -version
```

## Download and Install
Pick the latest elasticsearch download link using this link : [Download](https://www.elastic.co/downloads/elasticsearch)

Recommended to use tarball if you are a beginner

```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.5.0.tar.gz
tar -xzf elasticsearch-5.5.0.tar.gz
cd elasticsearch-5.5.0/
//get familiarized with directory structure, specifically bin and config
```

## Getting Started with Elastic Search
Start elastic search.
```
./bin/elasticsearch

//You may also open this url in browser.
//To verify the installation and setup.
curl -XGET http://localhost:9200

//You should get something like this in command line
{
  "name": "f7RZM1d",
  "cluster_name": "elasticsearch",
  "cluster_uuid": "Lg8G6Yv7R8qMFJiJNNaWmw",
  "version": {
    "number": "5.5.0",
    "build_hash": "260387d",
    "build_date": "2017-06-30T23:16:05.735Z",
    "build_snapshot": false,
    "lucene_version": "6.6.0"
  },
  "tagline": "You Know, for Search"
}
```
Congratulations! You have completed the first step!

## Kibana Installation
Pick the latest kibana download link using this link : [Download](https://www.elastic.co/downloads/kibana)

```
wget https://artifacts.elastic.co/downloads/kibana/kibana-5.5.0-linux-x86_64.tar.gz
tar -xzf kibana-5.5.0-linux-x86_64.tar.gz
cd kibana/
./bin/kibana

//You should see this log in console
//log   [00:35:35.985] [info][listening] Server running at http://localhost:5601
```

Congratulations! You have setup Kibana and ready to use elasticsearch with this awesome tool!

# Elastic Search basics - commands
Please go through the following [video](https://www.elastic.co/webinars/getting-started-elasticsearch). 

This is a very good first tutorial on elastic search and Kibana. The commands used in this video is listed here.

Open kibana http://localhost:5601 and go to Dev Tools

Useful commands
```
GET /

POST /my-index/my-type/1
{
    "body": "foo"
}

GET /my-index/my-type/1

GET /my-index/_search
{
    "query": {
        "match": {
            "body": "foo"
        }
    }
}

DELETE /my-index/my-type/1
```

# X-Pack Installation
Stop elasticsearch and kibana first.
Install plugins and then start both elasticsearch and kibana back again

```
//Ctrl + C to exit elasticsearch and kibana
//Run inside elasticsearch directory
./bin/elasticsearch-plugin install x-pack
./bin/elasticsearch

//Inside kibana directory
./bin/kibana-plugin install x-pack
./bin/kibana
```

Kibana x-pack installation includes setting up the bundles, and typically takes some time. Please be patient.
After kibana startup, you should see login page now at http://localhost:5601.

Default username = `elastic` and password = `changeme` 

You can see more functionalities added to Kibana UI. For example, Monitoring and Graph.

```
# Start by creating the "library" index, an optional step

PUT /library
{
  "settings": {
    "index.number_of_shards": 1,
    "index.number_of_replicas": 0
  }
}


# Bulk indexing and Search
# When you need to index a lot of docs, you should use the bulk API

POST /library/books/_bulk
{"index": {"_id": 1}}
{"title": "The quick brown fox", "price": 5, "colors": ["red", "green", "blue"]}
{"index": {"_id": 2}}
{"title": "The quick brown fox jumps over the lazy dog", "price": 15, "colors": ["blue", "yellow"]}
{"index": {"_id": 3}}
{"title": "The quick brown fox jumps over the quick dog", "price": 8, "colors": ["red", "blue"]}
{"index": {"_id": 4}}
{"title": "Brown fox brown dog", "price": 2, "colors": ["black", "yellow", "red", "blue"]}
{"index": {"_id": 5}}
{"title": "Lazy dog", "price": 9, "colors": ["red", "blue", "green"]}

# More info: https://www.elastic.co/guide/en/elasticsearch/guide/current/bulk.html


# Find all documents

GET /library/books/_search


# Query example : find all fox

GET /library/books/_search
{
  "query": {
    "match": {
      "title": "fox"
    }
  }
}


# Query example : "quick" and "dog"

GET /library/books/_search
{
  "query": {
    "match": {
      "title": "quick dog"
    }
  }
}


# Query example: match_phrase = exact match

GET /library/books/_search
{
  "query": {
    "match_phrase": {
      "title": "quick dog"
    }
  }
}


# Have a look at the relevance (.score) : using BM25 algorithm

GET /library/books/_search
{
  "query": {
    "match": {
      "title": "quick"
    }
  }
}


# Boolean query combinations : "must" match.

GET /library/books/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "title": "quick"
          }
        },
        {
          "match_phrase": {
            "title": "lazy dog"
          }
        }
      ]
    }
  }
}


# Boolean query combinations : "must_not" example.

GET /library/books/_search
{
  "query": {
    "bool": {
      "must_not": [
        {
          "match": {
            "title": "lazy"
          }
        },
        {
          "match_phrase": {
            "title": "quick dog"
          }
        }
      ]
    }
  }
}


# Boolean query combinations : "should" = not necessarily a "must", but scoring affected by matches.

GET /library/books/_search
{
  "query": {
    "bool": {
      "should": [
        {
          "match_phrase": {
            "title": {
              "query": "quick dog"
            }
          }
        },
        {
          "match_phrase": {
            "title": {
              "query": "lazy dog",
              "boost": 3
            }
          }
        }
      ]
    }
  }
}


# "highlight" example. Very useful in autocomplete dropdowns.

GET /library/books/_search
{
  "query": {
    "bool": {
      "should": [
        {
          "match_phrase": {
            "title": {
              "query": "quick dog",
              "boost": 2
            }
          }
        },
        {
          "match_phrase": {
            "title": {
              "query": "lazy dog"
            }
          }
        }
      ]
    }
  },
  "highlight": {
    "fields": {
      "title": {}
    }
  }
}


# "filter" example. Filter documents based on criteria

GET /library/books/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "title": "dog"
          }
        }
      ],
      "filter": {
        "range": {
          "price": {
            "gte": 5,
            "lte": 10
          }
        }
      }
    }
  }
}


# "filter" example, no query.

GET /library/books/_search
{
  "query": {
    "bool": {
      "filter": {
        "range": {
          "price": {
            "gt": 5
          }
        }
      }
    }
  }
}


# "analyze" examples. 

GET /library/_analyze
{
  "tokenizer": "standard",
  "text": "Brown fox brown dog"
}

GET /library/_analyze
{
  "tokenizer": "standard",
  "filter": ["lowercase"],
  "text": "Brown fox brown dog"
}

GET /library/_analyze
{
  "tokenizer": "standard",
  "filter": ["lowercase", "unique"],
  "text": "Brown brown brown fox brown dog"
}

GET /library/_analyze
{
  "analyzer": "standard",
  "text": "Brown fox brown dog"
}

GET /library/_analyze
{
  "analyzer": "standard",
  "filter": ["lowercase"],
  "text": "THE quick.brown_FOx Jumped! $19.95 @ 3.0"
}

GET /library/_analyze
{
  "tokenizer": "letter",
  "filter": ["lowercase"],
  "text": "THE quick.brown_FOx Jumped! $19.95 @ 3.0"
}

GET /library/_analyze
{
  "tokenizer": "standard",
  "text": "elastic@example.com website: https://www.elastic.co"
}

GET /library/_analyze
{
  "tokenizer": "uax_url_email",
  "text": "elastic@example.com website: https://www.elastic.co"
}


# "aggs" examples. Aggregations

GET /library/_search
{
  "size": 0,
  "aggs": {
    "popular-colors": {
      "terms": {
        "field": "colors.keyword"
      }
    }
  }
}


# "aggs" examples. Aggregations with filters

GET /library/_search
{
  "query": {
    "match": {
      "title": "dog"
    }
  },
  "aggs": {
    "popular-colors": {
      "terms": {
        "field": "colors.keyword"
      }
    }
  }
}


# "aggs" examples. Simple and nested aggregations.

GET /library/_search
{
  "size": 0, 
  "aggs": {
    "price-statistics": {
      "stats": {
        "field": "price"
      }
    },
    "popular-colors": {
      "terms": {
        "field": "colors.keyword"
      },
      "aggs": {
        "avg-price-per-color": {
          "avg": {
            "field": "price"
          }
        }
      }
    }
  }
}


# document update. versions get upgraded during each update.

POST /library/books/1
{
  "title": "The quick brown fox",
  "price": 10,
  "colors": ["red", "green", "blue"]
}

POST /library/books/1/_update
{
  "doc": {
    "title": "The quick fantastic fox"
  }
}

GET /library/books/1


# "_mapping". Elasticsearch will dynamically define your schema when documents are indexed

GET library/_mapping


# Define index setting when it is created

PUT /famous-librarians
{
  "settings": {
    "index": {
      "number_of_shards": 2,
      "number_of_replicas": 0,
      "analytics": {
        "analyzer": {
          "my-desc-analyzer": {
            "type": "custom",
            "tokenizer": "uax_url_email",
            "filters": ["lowercase"]
          }
        }
      }
    }
  },
  "mappings": {
    "librarian": {
      "properties": {
        "name": {
          "type": "text"
        },
        "favorite-colors": {
          "type": "keyword"
        },
        "birth-date": {
          "type": "date",
          "format": "year_month_day"
        },
        "hometown": {
          "type": "geo_point"
        },
        "description": {
          "type": "text",
          "analyzer": "my-desc-analyzer"
        }
      }
    }
  }
}

PUT /famous-librarians/librarian/1
{
  "name": "Sarah Byrd Askew",
  "favorite-colors": ["yellow", "light-grey"],
  "birth-date": "1877-02-15",
  "hometown": {
    "lat": 32.349722,
    "lon": -87.641111
  },
  "description": "An American public librarian who pioneered the establishment of country libraries in the United States - https://en.wikipedia.org/wiki/Sarah_Byrd_Askew"
}

PUT /famous-librarians/librarian/2
{
  "name": "John L. Beckley",
  "favorite-colors": ["red", "off-white"],
  "birth-date": "1757-08-07",
  "hometown": {
    "lat": 51.507222,
    "lon": -0.1275
  },
  "description": "An American political campaign manager and the first librarian of the United States Congress - https://en.wikipedia.org/wiki/John_J._Beckley"
}
```