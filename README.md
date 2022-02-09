# Complete Guide To Elasticsearch

1. Cluster contains multiple nodes.

1. When a node is created either it will join a existing cluster if configured or create a own cluster for it self

1. Each unit of data that stored with in cluster is called document.

1. When you index a document, the original JSON object that you sent to Elasticsearch is stored along with some meta data that elastciserach uses internally.

1. Documents are organized with in indices. Every document within Elasticsearch is stored within an index.

1. Search queries are run against indices.

1. Clusters to divide the components logically.

1. Single node problems like scalability and availability.

1. Example Document

```json
{
    _index: "people"
    _type: "_doc"
    _id: "123"
    _version": 1
    _source: {
        name: "Harish"
        age: 30
    }
}
````

1. Documents are organized with in index.

1. Index is a collections of documents and logically related.

1. Index can have as many of documents, there is no hard limit.

## Inspecting Cluster

1. Cluster Health

GET /_cluster/health

1. APIs with _, commands health.

1. Node information GET _cat/nodes.

1. Nodes API _nodes

1. Indicies API _cat/indicies.

## Sharding and Scalability

1. Sharding is a way to divide indices into smaller pieces

1. Each piece is referred to as a shard

1. Sharding is done at the index level

1. The main purpose is to horizontally scale the data volume

1. A shard is an independent index.. kind of.

1. Elasticsearch is build on top of Apache Luncene index.

1. An Elasticsearch index consists of one or more Lucene indices.

1. A shard has no predefined size; it grows as documents are added to it.

1. A shard may store up to about two billion documents

1. Mainly to be able to store more documents.

1. To easier fit large indices onto nodes

1. Improved preformance
    - Parallelization of queries increases the throughput of an index.

## Configuring the number of shards

1. An index contains a single shard by default

1. Indices in Elasticsearch < 7.0.0 we created with Five shards
    - This often led to over-sharding

1. We can increase the number of shars with Split API.

1. To reduce the number of shards with the Shrink API.

## Understanding Replication

1. Elasticsearch supports replication for fault tolerance

1. Replication is supported natively and enabled by default

1. Replication is configured at the index level

1. Replication works by creating copies of shards, referred to as replica shards.

1. Replica shards are stored on different node.

1. Replica shards of a replication group can serve different search requests simultaneously.

1. Elasticsearch intelligently routes requests to the best shard.

1. CPU parallelization improves preformance in mutliple replica shards are stored on the same node.


### Create a new index

1. PUT /pages will cerate a new index with default settings.

1. As we have only one node, replica shard is not assigned to any node which changed the health of the cluster to yellow.

1. GET _cat/shards?v

### Node Roles

1. Master-eligible
1. Data role
    1. Enables a node to store data
    1. Storing data includes releated to that data, such as search queries

1. Ingest
    1. Enables a node to run ingest pipelines

1. Machine Learning
    1. node.ml identifies a node as machine learning node

1. Coordination
    1. Coordination refers to the distribution of queries and the aggregation of results.
    1. Configured by disabling all other roles

1. Voting-only
    1. Rarely used, and you almost certainly won't use it either

## Understanding routing

1. Routing is the process of resolving shard for a document

```sh
shard_num = hash(_routing) % num_primary_shards
```

## How Elasticserch reads data

1. Routing plays a key role in reading the data

1. Coordination node gets the request, it will find the shard by using routing.

1. Elasticsearch uses Adative replica selection(ARS) for finding the replication group to find the best shard for reading the data from.

1. The response from the shard is send to coordination node which sends the response to client.

## Writing Data

1. Coordination node will get the request, which finds the Primary Shard to write data.

1. Primary shard will validate the request, validating structure, field values.

1. Then performs the write operation locally, to improve the operation primary shard will forward the request to replicas parallely.

1. Primary terms and sequence numbers are key when Elasticsearch needs to recover from a primary shard failure.

1. For large indices, this process is really expensive.

1. Global and local checkpoints

## Document Versioning

1. Not a revision history of documents

1. _version

## Optimistic Concurrency Control

1. Prevent overwriting documents indavently due to concurrent operations by using _primary_term and _seq_no
