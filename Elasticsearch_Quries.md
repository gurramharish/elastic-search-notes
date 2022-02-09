## Cluster Health

```sh
GET _cluster/health
```

## Nodes Details

```sh
GET _cat/nodes?v
```

## Nodes Details as JSON

```sh
GET _nodes
```

## Get avaialble indices

```sh
GET _cat/indices?v
```

## Search the index for document count

```sh
GET .kibana_7.16.3/_count
```

## Creating index with default settings

```sh
PUT /dummy_pages
```

## Get index settings and mappings

```sh
GET /dummy_pages
```

## Delete index

```sh
DELETE /dummy_pages
```

## Get Shards

```sh
GET _cat/shards?v
```

## Create Index with settings and mappings

```sh
PUT /products
{
  "settings": {
    "number_of_shards": 1
    , "number_of_replicas": 1
  },
  "mappings": {
    "properties": {
      "name": { "type": "text" },
      "price": { "type": "integer" },
      "in_stock": { "type": "integer" }
    }
  }
}
```

## Get all data from index

```sh
GET /products/_search
```

## Add data to the index first time

```sh
POST /products/_doc
{
  "name": "Coffee Maker",
  "price": 49,
  "in_stock": 20
}
```

## Update data in the index with new product or existing product

```sh
PUT /products/_doc/101
{
  "name": "Bread Toaster",
  "price": 10,
  "in_stock": 30
}

or

POST /products/_update/101
{
  "doc": {
    "name": "Toaster"
  }
}
```

## Get Data using id

```sh
GET /products/_doc/101
```

## Scripted Updates

```sh
POST /products/_update/101
{
  "script": {
    "source": "ctx._source.in_stock--"
  }
}

POST /products/_update/101
{
  "script": {
    "source": "ctx._source.in_stock = 10"
  }
}

POST /products/_update/101
{
  "script": {
    "source": "ctx._source.in_stock -= params.quantity",
    "params": {
      "quantity": 4
    }
  }
}

# Updating multiple variables using script
POST /products/_update/101
{
  "script": {
    "source": "ctx._source.in_stock -= params.quantity;ctx._source.name = params.name",
    "params": {
      "quantity": 4,
      "name": "Telsa Toaster"
    }
  }
}

# Stopping the update based on the condition
POST /products/_update/101
{
    "script": {
        "source": """
            if(ctx._source.in_stock == 0) {
                ctx.op = 'noop';
            }
            ctx._source.in_stock--;
        """
    }
}

# Delete while updating based on condition using script
POST /products/_update/101
{
  "script": {
    "source": """
      if(ctx._source.in_stock <= 1) {
        ctx.op = "delete";
      }
      ctx._source.in_stock--
    """
  }
}
```

## Upserts update if present else insert

```sh
POST /products/_update/201
{
  "script": {
    "source": "ctx._source.in_stock++"
  },
  "upsert": {
    "name": "Samsung Galaxy S9+",
    "price": 1000,
    "in_stock": 20,
    "tags": ["electronics", "mobile", "gadget"]
  }
}
```

## Replace Documents

```sh
# Will replace the whole document with the body provided

PUT /products/_doc/101
{
  "name": "Toaster",
  "price": 79,
  "in_stock": 10
}
```

## Delete document

```sh
DELETE /products/_doc/101
```

## Update with optimistic cocurrent lock

```sh
POST /products/_doc/111?if_primary_term=4&if_seq_no=47
{
  "name": "T-Link 2.5Hzs",
  "price": 9888.43,
  "in_stock": 30
}
```

## Update Mutliple documents by query

```sh
POST /products/_update_by_query
{
  "script": {
    "source": "ctx._source.in_stock--",
    "lang": "painless"
  },
  "query": {
    "match_all": {}
  }
}
```

## Delete by query

```sh
POST /products/_delete_by_query
{
  "query": {
    "match_all": {}
  }
}
```

## Batch Processing(Bulk API Using NDJSON Specification)

1. Actions for bulk api create, index, update, delete

1. Each action should be inside json in single line followed by json body

1. Delete action dont need to have json body followed by action.

1. The HTTP Content-Type header should be set as follows
    * Content-Type: application/x-ndjson

1. Kiban handles the Content-Type for us.

1. Each line must end with newline character (\n or \r) for last line also.

1. A failed action will not affect the other actions.

```sh
POST /_bulk
{ "index": { "_index": "products", "_id": 201 } }
{ "name": "Apple MacBook Pro", "price": 1000, "in_stock": 5 }
{ "create": { "_index": "products", "_id": 202 } }
{ "name": "Milton Water Can", "price": 20, "in_stock": 20 }


POST /products/_bulk
{ "update": { "_id": 201 } }
{ "doc": { "price": 999 } }
{ "delete": { "_id": 202 } }
```
