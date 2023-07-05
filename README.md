Ref.: https://www.kloia.com/blog/kafka-connect-elasticsearch-sink-connector

## Installation

```
// start Docker network and conatiners in docker-compose.yml
$ docker-compose up -d

// Configure kafka-connect with ElasticSearch sink
$ curl -X POST http://localhost:8083/connectors -H "Content-Type: application/json" -d @connector-setup_payload.json

// Load sample data locally into the Kafka instance
$ docker exec -i kafka bash -c "echo '{\"request\": {\"userId\" : \"23768432478278\"}}' | /opt/kafka/bin/kafka-console-producer.sh --broker-list kafka:9092 --topic example-topic"

```

## Tests

```
// get all ElasticSearch indices
$ curl -X GET "localhost:9200/_cat/indices/*?v=true&s=index&pretty"

// get the data from the Kafka 'example-topic' as index query in ElasticSearch
$ curl http://localhost:9200/example-topic/_search\?pretty
{
  "took" : 7,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 2,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "example-topic",
        "_type" : "_doc",
        "_id" : "example-topic+0+0",
        "_score" : 1.0,
        "_source" : {
          "request" : {
            "userId" : "23768432478278"
          },
          "messageTS" : "2023-07-05T13:31:08"
        }
      },
      {
        "_index" : "example-topic",
        "_type" : "_doc",
        "_id" : "example-topic+0+1",
        "_score" : 1.0,
        "_source" : {
          "request" : {
            "userId" : "23768432478278"
          },
          "messageTS" : "2023-07-05T13:31:41"
        }
      }
    ]
  }
}

```


