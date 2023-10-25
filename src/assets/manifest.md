---
schemaVersion: 1
title: GraphQL
id: graphql
repo: https://github.com/fastly/graphql-compute-example
editUrl: https://github.com/fastly/graphql-compute-example/edit/main/src/assets/manifest.md
image:
  href: /logo.png
  alt: GraphQL
description: |
  Run a GraphQL Helix server at the edge with Fastly Compute to scale to huge amounts of queries
views:
  endUser:
    mode: frame
    href: /
    height: 800
sessions: true
---

## What is GraphQL?

GraphQL is a query language for APIs and a runtime for fulfilling those queries with your existing data. GraphQL provides a complete and understandable description of the data in your API, gives clients the power to ask for exactly what they need and nothing more, makes it easier to evolve APIs over time, and enables powerful developer tools.
[Find out more about GraphQL](https://graphql.org/)

## Why run GraphQL at the edge?

Running GraphQL at the edge allows you to move the processing as close to your users as possible and cache any requests sent to the origin to improve speed and reduce origin load.

### How does it work?

This is a Javascript app running inside of Fastly using the [GraphQL Helix](https://github.com/contra/graphql-helix) npm module to process and resolve incoming queries.
Data is pulled from a [fake api data source](https://jsonplaceholder.typicode.com).

#### Defining Data sources

GraphQL is configured by adding queries and mutations to the [schema file](https://github.com/fastly/graphql-compute-example/blob/main/src/schema.js).
This is useful as one GraphQL service can pull data from many different APIs.

#### Caching

Normally it is very difficult to achieve good caching when using GraphQL because all requests are POST requests, and if the edge server passes the query directly to a GraphQL API upstream, the response is hard to cache (there are workarounds, see [this example](https://developer.fastly.com/solutions/examples/cache-graphql-query-response/) for a simplistic solution if you are using VCL and unable to migrate to Compute).

However, by using Compute, we can process GraphQL requests at the edge with full knowledge and understanding of the GraphQL semantics, and make use of Fastly's caching mechanisms in the most appropriate way. This drastically lowers the load on APIs, because when queries are processed, they can be resolved with cached data.  An example of caching responses from origin can be found in the demo code [here](https://github.com/fastly/graphql-compute-example/blob/main/src/schema.js#L75), but would benefit from being more sophisticated for a production deployment.

### Trying it out

Try getting a list of all users with the query below

```
query {
  users {
    id
    name
    email
    address {
      city
    }
    company {
      name
    }
  }
}
```

Or get a list of all posts with this query

```
query {
  posts {
    id
    title
    userId,
    body
  }
}
```
