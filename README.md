<p align="center">
  <big><strong>Cardano GraphQL</strong></big>
</p>

<p align="center">
  <img width="200" src=".github/images/cardano-logo.png"/>
</p>

[![CI][img_src_CI]][workflow_CI]

<hr/>

## Overview

Cross-platform, _typed_, and **queryable** API for Cardano. The project contains multiple [packages] for composing 
GraphQL services to meet specific application demands, and a [docker-compose stack] serving the included 
[cardano-graphql-server Dockerfile], the extended [hasura Dockerfile], [cardano-node-ogmios]. The [schema] is defined in
native `.graphql`, and used to generate a [TypeScript package for client-side static typing]. A mutation is available to 
submit a signed and serialized transaction to the local node.
 
[Apollo Server] exposes the NodeJS execution engine over a HTTP endpoint, and includes support for open source metrics
via Prometheus, and implementing operation filtering to deny unexpected queries. Should you wish to have more control
over the server, or stitch the schema with an existing service, consider importing the executable schema from the 
`@cardano-graphql/api-*` packages only.

**GraphQL** is a query language and execution environment with server and client implementations across many programming
languages. The language can be serialized for network transmission, schema implementations hashed for assurance, and is
suited for describing most domains.
 
**TypeScript** (and JS) has the largest pool of production-ready libraries, developers, and interoperability in the
GraphQL and web ecosystem in general. TypeScript definitions for the schema, generated by [GraphQL Code Generator], are
[available on npm].

**[Ogmios]** is a protocol translation service written in Haskell running on top of cardano-node. It offers a JSON
interface through WebSockets and enables applications to speak Ouroboros' client mini-protocols via remote procedure
calls.

## Getting Started
Check the [releases] for the latest version.
``` console
git clone \
  --single-branch \
  --branch <VERSION> \
  --recurse-submodules \
  https://github.com/input-output-hk/cardano-graphql.git \
  && cd cardano-graphql
```
### Build and Run via Docker Compose
Builds `@cardano-graphql/server` and starts it along with `cardano-ogmios-node`, `cardano-db-sync-extended`, `postgresql`, and `hasura`:

``` console
DOCKER_BUILDKIT=1 COMPOSE_DOCKER_CLI_BUILD=1 docker-compose up -d --build && docker-compose logs -f
```
:information_source: _Omit the `--build` to use a pre-built image from Dockerhub (or locally cached from previous build)_
### Check Cardano DB sync progress
Use the GraphQL Playground in the browser at http://localhost:3100/graphql:
``` graphql 
{ cardanoDbMeta { initialized syncPercentage }}
```
or via command line:
``` console
curl \
  -X POST \
  -H "Content-Type: application/json" \
  -d '{"query": "{ cardanoDbMeta { initialized syncPercentage }}"}' \
  http://localhost:3100/graphql
```
:information_source: Wait for `initialized` to be `true` to ensure the epoch dataset is complete. After the first sync
you may need to restart the services using `docker-compose restart cardano-graphql` if the GraphQL server isn't running.
### Query the full dataset
```graphql
{ cardano { tip { number slotNo epoch { number } } } }
```
``` console
curl \
  -X POST \
  -H "Content-Type: application/json" \
  -d '{"query": "{ cardano { tip { number slotNo epoch { number } } } }"}' http://localhost:3100/graphql
```
### :tada:
``` json
{ "data": { "cardano": { "tip": { "number": 4391749, "slotNo": 4393973, "epoch": { "number": 203 } } } } }
```

For more information, have a look at the [Wiki :book:].

## How to install (Linux / Docker)

### Docker

See [Using Docker].

### From Source 

See [Building].

## Documentation

| Link                                                                                               | Audience                                                     |
| ---                                                                                                | ---                                                          |
| [API Documentation]                                                                                | Users of the Cardano GraphQL API                             |
| [Wiki :book:]                                                                                      | Anyone interested in the project and our development process |
| [Example Queries - Cardano DB Hasura]        | Users of the Cardano DB Hasura API                             |

<hr/>

<p align="center">
  <a href="https://github.com/input-output-hk/cardano-graphql/blob/master/LICENSE"><img src="https://img.shields.io/github/license/input-output-hk/cardano-graphql.svg?style=for-the-badge" /></a>
</p>

[img_src_CI]: https://github.com/input-output-hk/cardano-graphql/workflows/CI/badge.svg
[workflow_CI]: https://github.com/input-output-hk/cardano-graphql/actions?query=workflow%3ACI
[packages]: ./packages
[docker-compose stack]: ./docker-compose.yml
[cardano-graphql-server Dockerfile]: ./Dockerfile
[hasura Dockerfile]: ./packages/api-cardano-db-hasura/hasura/Dockerfile
[cardano-node-ogmios]: https://ogmios.dev/getting-started/docker/
[schema]: ./packages/api-cardano-db-hasura/schema.graphql
[TypeScript package for client-side static typing]: ./packages/client-ts/README.md
[Apollo Server]: https://www.apollographql.com/docs/apollo-server/
[GraphQL Code Generator]: https://graphql-code-generator.com
[available on npm]: https://www.npmjs.com/package/cardano-graphql-ts
[Ogmios]: https://ogmios.dev/
[releases]: https://github.com/input-output-hk/cardano-graphql/releases
[Wiki :book:]: https://github.com/input-output-hk/cardano-graphql/wiki
[Using Docker]: https://github.com/input-output-hk/cardano-graphql/wiki/Docker
[Building]: https://github.com/input-output-hk/cardano-graphql/wiki/Building
[API Documentation]: https://input-output-hk.github.io/cardano-graphql
[Example Queries - Cardano DB Hasura]: ./packages/api-cardano-db-hasura/src/example_queries