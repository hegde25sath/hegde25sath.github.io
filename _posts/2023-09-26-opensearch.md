---
title: "Overview and Notes on OpenSearch"
date: 2023-09-26 21:33:00
categories: []
tags: [computing,notes]
math: true
---

## Purpose 
These rough notes compiled from the various sources listed in the end may be a good introduction to anyone looking to contribute to OpenSearch projects.

Please open an issue if something needs to be phrased better or a section needs to be added/removed.

## Overview: What is Open Search?

- **Distributed** search & analytics engine - you feed data into OpenSearch and can perform search on it for various things. 
    - Can visualize information using OpenSearch Dashboards

#### Use Cases

- Log analytics, trace analytics, anomaly detection, website search

### Distributed?

- Organized into clusters which are made up of 1 or more nodes 
- A node = running instance of OpenSearch
    - Basically servers that store data & process search requests - best practice is one instance (node) per server
- Cluster = collection of nodes running OpenSearch

### Data organization

- Data organized into *indices* = collection of JSON *documents* which are the foundational/basic elements. E.g.:

```JSON
{
    "title": "The Wind Rises",
    "release_date": "2013-07-20",
}
```

- Before adding to OpenSearch, need to convert data into above format. 
- After adding document to index (indexing a document), some metadata is inserted by OpenSearch:

```JSON
{
  "_index": "<index-name>",
  "_type": "_doc",
  "_id": "<document-id>",
  "_version": 1,
  "_source": {
    "title": "The Wind Rises",
    "release_date": "2013-07-20"
  }
}
```

### Shards: Primary and Replica

#### Relationship between index & shards

- An index can have many shards, but each shard only belongs to one index.
- Indices split into shards and distributed across nodes in a cluster.
- each doc stored in 1 primary shard - when doc indexed, first indexed on primary shard, then on all its replicas. 
    - each shard is a standalone search engine; piece of an OpenSearch index, but also a full Apache Lucene index.

#### Primary Shards
- Essentially the number of ways data is split up
- If $X$ primary shards, each shard will have $\frac{1}{X}$  of the data
    - Enables queries to be performed in parallel, thus faster.
    - There's diminishing returns for increasing number of primary shards however. 

#### Replica Shards

- *Replica* shard made for each primary shard - which act as redundancy/backup if primary shard's node fails.
    - Replica shards are in different nodes, not the one corresponding primary shard is at.
    - Default is one replica for each primary, but can be changed without reindexing. 
    - Also improves performance since get and search requests can be handled by primary and replica.

### Rest API and Open Search

#### Overview of RESTful API's
- API: way for programs to talk to each other
- Client gets resource by making a request at the endpoint URI, and server sends a response after doing some processing.
- Stateless - don't need to store things.

#### Specifics
- Interaction with clusters is through REST API - to add new document, get statistics, delete documents, etc. 
- To add JSON doc to OpenSearch index (i.e. to index the document), HTTP request sent at specific URI:

```JSON
{
    "title": "The Wind Rises",
    "release_date": "2013-07-20"
}
```

## Index State Management (ISM) Plugin

### Motivation
- Sometimes new data is more valuable than older data.
    - E.g., time series data of oil prices. 
- To save space and costs might need to reduce replica shards for older indexes and so on.
-  ISM plugin enables automation of such tasks through defining **policies** 
    -  Can for example define policy to make index `read_only` or delete it altogether after a set amount of time.
    -  Notifications for these actions can be set as well. 
  

## References
<https://opensearch.org/docs/latest/about/>

<https://bonsai.io/blog/what-is-opensearch>

<https://opensearch.org/docs/latest/im-plugin/ism/index>

<https://opensearch.org/docs/latest/im-plugin/ism/index>

## Further Reading

### Shards

<https://opensearch.org/blog/optimize-opensearch-index-shard-size/>

<https://opster.com/guides/opensearch/opensearch-basics/opensearch-shards/>

### Indicies

<https://opensearch.org/docs/latest/im-plugin/index/>

<https://opster.com/guides/opensearch/opensearch-basics/opensearch-indexing/>

<https://opster.com/guides/opensearch/opensearch-basics/opensearch-index/>

### Other

<https://opster.com/guides/opensearch/opensearch-basics/document/>

<https://opster.com/guides/opensearch/opensearch-basics/opensearch-search/>