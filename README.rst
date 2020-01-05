# PostgreSQL to Elasticsearch sync

[PGSync](https://pgsync.com) is a middleware for syncing data from [Postgres](https://www.postgresql.org) to [Elasticsearch](https://www.elastic.co/products/elastic-stack).  
It allows you to keep [Postgres](https://www.postgresql.org) as your source of truth data source and
expose structured denormalized documents in [Elasticsearch](https://www.elastic.co/products/elastic-stack).

### Requirements

- [Python](https://www.python.org) 3.6+
- [Postgres](https://www.postgresql.org) 9.4+
- [Redis](https://redis.io) 3.1.0+
- [Elasticsearch](https://www.elastic.co/products/elastic-stack) 6.3.1+

### Postgres setup
  
  Enable [logical decoding](https://www.postgresql.org/docs/current/logicaldecoding.html) in your 
  Postgres setting.

  - you would also need to set up at least two parameters at postgresql.conf

    ```wal_level = logical```

    ```max_replication_slots = 1```

### Installation

You can install PGSync from [PyPI](https://pypi.org/project/realpython-reader/):

    $ pip install pgsync

### Config

Create a schema for the application named e.g **_schema.json_**

Example configuration

```json
[
    {
        "index": "[database name]",
        "nodes": [
            {
                "table": "[table A]",
                "columns": [
                    "column 1 from table A",
                    "column 2 from table A",
                    ... additional columns
                ],
                "children": [
                    {
                        "table": "[table B with relationship to table A]",
                        "columns": [
                          "column 1 from table B",
                          "column 2 from table B",
                          ... additional columns
                        ],
                        "relationship": {
                            "variant": "object",
                            "type": "one_to_many"
                        },
                        ...
                    },
                    {
                        ... any other additional children
                    }
                ]
            }
        ]
    }
]
```

### Environment variables 

Setup required environment variables for the application

    SCHEMA='/path/to/schema.json'

    ELASTICSEARCH_HOST=localhost
    ELASTICSEARCH_PORT=9200

    PG_HOST=localhost
    PG_USER=i-am-root # this must be a postgres superuser
    PG_PORT=5432
    PG_PASSWORD=*****

    REDIS_HOST=redis
    REDIS_PORT=6379
    REDIS_DB=0
    REDIS_AUTH=*****


## Running

  - bootstrap the database
        $ bootstrap --config schema.json
  - run pgsync as a daemon
        $ pgsync --config schema.json -d