---
layout: default
title: GraphQL Playground
description: A blog all about GraphQL Playground
permalink: /stargate/graphql/playground/
---

# What is GraphQl Playground?

Bundled within the docker stargate is an instance of GraphQL Playground.  This UI provides an interface to execute GraphQL queries against Stargate & Astra APIs.

# Why use GraphQL Playground?

This is a great tool to quickly get started testing GraphQL queries.  It also comes with a great share feature allowing you to share your work with team members.

# Working with GraphQL Playground

With Stargate Docker running you can access GraphQL Playground with the following Url:

[http://localhost:8080/playground](http://localhost:8080/playground)

Here are few things I learned:
*	Your changes in this UI will not persist so do not refresh the page.  If you want to save your work, your only option is to use Share Option which creates a [graphqbin.com](graphqbin.com) version of your workspace.
*	Check out HTTP HEADERS (bottom right), you'll need your auth token, and also notice the case change here from "X-Cassandra-Token" to "x-cassandra-token".  You will have to update this every tab.
*	When working on calls, check out the Copy Curl button.  This will expose the exact curl call you are executing under the hood.  I have used this extensively.
*	A new tab will not have your auth token prefilled nd you will need to adjust the url for your API endpoint.
*   Query variables do work.


# GraphQL Playground Posts
<p>&nbsp;</p>
<div class="mui-container">
  <div class="home mui-row">
      <div class="mui-col-md-2">
        <article class="mui-panel">
          <div class="bg-holder bg-deep-purple" style="background:url(/assets/images/database.jpg) no-repeat center center">
            <span class="post-list-title">
              <a class="post-link" href="/stargate/graphql/playground/day/1/">GraphQl Day 1</a>
            </span>
            <div class="img-overlay"></div>
            <a href="/stargate/graphql/playground/day/1/" class="overlay"></a>
          </div>
          <div class="post-data">
            <p class="post-excerpt">My first day working in the GraphQL Playground UI</p>
            <p><b>tags:</b> graphql, playground, export, tabs, testing, ui</p>
          </div>
        </article>
      </div>
  </div>

  {% include stargate_help.html %}