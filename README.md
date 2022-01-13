# Spacework Backend System Design

I imported this project to my github repos from my original project in a github organization for code cleanup and visibility purposes. The original project with the original commit history can be found [here](https://github.com/sdc-wework/photos-service).

## Table of Contents

1. [Motivation and Constraints](#motivation)
1. [System design and containerization strategy](#system-design)
1. [Horizontal scaling results](#results)
1. [Design advantages and possible enhancements](#design-advantages)
1. [REST API Reference](#rest-api)
   1. [Endpoints and available HTTP request methods](#endpoints)

## Motivation

To design a backend system that supports millions of records and that is capable of handling a large amount of request per second with low latenacy and a minimal amount of errors.

## Constraints

Unable to vertically scale as the project has to be as cost effective as possible.

## System design

![System Design](https://raw.githubusercontent.com/aleksebastian/spacework-photos-service/main/systemDesignFiles/systemDesign.png)

## Containerization strategy

![Conteinarization Strategy](https://raw.githubusercontent.com/aleksebastian/spacework-photos-service/main/systemDesignFiles/containerization.png)

## Results

### Prior to horizontal scale - 2400ms average for 400 requests per second

![Before horizontally scaling](https://raw.githubusercontent.com/aleksebastian/spacework-photos-service/main/systemDesignFiles/startPerfomance.png)

### Post horizontal scale - 27ms average for 3400 requests per second

![After horizontally scaling](https://raw.githubusercontent.com/aleksebastian/spacework-photos-service/main/systemDesignFiles/finalPerformance.png)

## Design advantages

- Microcaching keeps data fresh but also allows for the optimal distribution of resources given the T2 micro small capabilities. It strikes a balance of cache HITS and MISSES that prevents the NGINX cache from overloading with requests.
- A Redis cache allows for extremely fast retrieval of data further protecting the database from operational overload.
- CouchDB with indexed IDs performs read operations consistently sub 6ms.

## Possible enhancements

- The bottleneck is in the CouchDB database when too many read operations occur. To help with this bottleneck a cluster of CouchDB databases could be set-up and deployed so that the read operations are distributed across more than one database intance.

## REST API

### Endpoints

Photos `/api/photos`

Workspace `/api/photos/workspace`

### HTTP request methods

#### GET

    Photo data by photo id - /api/photos/:workspaceId/:id

    Photos data by workspace id - /api/photos/workspace/:workspaceId

#### POST

    New photo data - /api/photos/workspace/:workspaceId

##### Request body example

```
  {
      "url": "http://www.imageurl.com/",
      "description": "Description goes here"
  }
```

---

#### PUT

    Update photo url and/or photo description - /api/photos/:workspaceId/:id

##### Request body example (can have url and/or description)

```
  {
      "url": "http://www.imageurl.com/",
      "description": "Description goes here"
  }
```

---

#### DELETE

    Photo data by photo id - /api/photos/:workspaceId/:id

    All workspace photo data - /api/photos/workspace/:workspaceId

---
