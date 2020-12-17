---
layout: default
title: Stargate
description: A blog all about Stargate
permalink: /stargate/
---

# What is Stargate?

Stargate is an open source api framework for data which makes the database administration task for any application more accessible by adding developer level support for all turnkey database operations.  Check out the [Stargate Website](https://stargate.io/), the [Stargate Github](https://github.com/stargate/stargate), and the [Stargate Documentation](https://stargate.io/docs/stargate/0.1/quickstart/quickstart.html).  If you are chatty they even have a 
[Discord](https://discord.com/invite/33mKDHHFUE).

# Why use Stargate?

Mostly because it is a portal to another future data world,  I use Stargate specifically for two futuristic use cases:
*   GraphQL + Cassandra
*   JSON Document Database   

# Learning Stargate

The best way for me to learn any new API is to read the documentation and build the samples.  Stargate was no exception with 3 different APIs I need to learn:

1. The REST API for CRUD calls such as: Create, Update, Read, Delete.  
2. The Document API for CRUD access to Documents Data in JSON.
3. The GraphQL API for interacting with Stargate using entire Graph Query Language feature. 

To give you a head start I have already created and tested everything in Postman.

# Check Out My Github Repo
Before getting started with Stargate & Postman download, fork, or clone my github repo which contains a Postman Environment and all of the sample calls you will find in the Stargate Documentation.   There is even a folder of sample calls for GraphQl.

{% include stargate_postman_repo.html %}

# GraphQL
One of the features I like the best about Stargate is the GraphQL capabilities.  I will come back here more with a /graphql/ page and more detail about the graphQL Api later. 

# GraphQl Playground
Bundled within the docker stargate is an instance of [GraphQL Playground](https://github.com/graphql/graphql-playground).  This UI provides a user interface (IDE UI) to execute GraphQL queries against Stargate GraphQL APIs.

<div class="mui-container">
    <div class="home mui-row">
      <div class="mui-col-md-6">
        <article class="mui-panel">
          <div class="bg-holder bg-deep-purple" style="background:url(/assets/images/database.jpg) no-repeat center center">
            <span class="post-list-title">
              <a class="post-link" href="/stargate/graphql/playground/">GraphQL Playground</a>
            </span>
            <div class="img-overlay"></div>
            <a href="/stargate/graphql/playground/" class="overlay"></a>
          </div>
          <div class="post-data">
            <p class="post-excerpt">My datastax experiences with GraphQL Playground</p>
          </div>
        </article>
    </div>
  </div>
</div>

{% include stargate_help.html %}