# Getting started with Elasticsearch

## An introduction to Elasticsearch

**Agenda**

 * Prerequisites
 * Download and installation
 * Getting started with Elastic Search
 * Kibana Installation

**Main Features**

 * Horizontal Scale
 * Real-Time Availability
 * Flexible Data Model
 * Rapid Query Execution
 * Sophisticated Query Language
 * Schemaless

## Prerequisites
Elastic search requires Java 8.

**Linux - Ubuntu**
Installing oracle java 8

```
sudo add-apt-repository -y ppa:webupd8team/java
sudo apt-get update
sudo apt-get -y install oracle-java8-installer
java -version
```

## Download and Install
Pick the latest elasticsearch download link using this link : [Download](https://www.elastic.co/downloads/elasticsearch)

```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.5.0.tar.gz
tar -xzf elasticsearch-5.5.0.tar.gz
cd elasticsearch-5.5.0/
//if you are a beginner, get familiarized with directory structure. Specifically bin and config
```

## Getting Started with Elastic Search
Start elastic search by going to into bin directory.
```
./bin/elasticsearch

//to verify the installation and setup
curl -XGET http://localhost:9200

//You should get something like this in the console
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

```
wget https://artifacts.elastic.co/downloads/kibana/kibana-5.5.0-linux-x86_64.tar.gz
tar -xzf kibana-5.5.0-linux-x86_64.tar.gz
cd kibana/
./bin/kibana

//You should see this log in console
//log   [00:35:35.985] [info][listening] Server running at http://localhost:5601
//Open this url in browser: default username=elastic; password=changeme
```

Congratulations! You have setup Kibana and ready to use elasticsearch with a visual tool.

# Elastic Search basics - commands
Please go through the following [video](https://www.elastic.co/webinars/getting-started-elasticsearch). 

This is a very good first tutorial on elastic search and Kibana. The commands used in this video is listed here.

```
GET /

PUT /library
{
  "settings": {
    "index.number_of_shards": 1,
    "index.number_of_replicas": 0
  }
}

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


GET /library/books/_search

GET /library/books/_search
{
  "query": {
    "match": {
      "title": "fox"
    }
  }
}

GET /library/books/_search
{
  "query": {
    "match": {
      "title": "quick dog"
    }
  }
}

GET /library/books/_search
{
  "query": {
    "match_phrase": {
      "title": "quick dog"
    }
  }
}

GET /library/books/_search
{
  "query": {
    "match": {
      "title": "quick"
    }
  }
}

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

GET library/_mapping

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
}

PUT /famous-librarians/librarian/1
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