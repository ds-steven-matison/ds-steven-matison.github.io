---
layout: default
title: Astra + Lagom
description: Connect your existing Lagom app to Astra
permalink: /astra/application/frameworks/lagom/
---

# How I Connected A Lagom App To Astra

Lagom was actually the first framework migration I was involved with here at Datastax.  Following [ankitp1342](https://github.com/Ankitp1342) lead I supported the full migration from a single 3 node Datastax Enterprise on-prem to multi-cluster multi-tentant infrastructure on Astra.  This application has been migrated and running on Astra (AWS) for over 3 months now.   Recently however a few Lagom developers responded on a [GitHub Issue](https://github.com/lagom/lagom/issues/3048) I created to ask Lagom to update their base driver version to 3.8 or higher.  Java based cassandra applications need the cassandra 3.8 or hire driver to connect to Astra.
Thank you [vcanuel](https://github.com/vcanuel) for the change details and motivation to add this blog page.

First we must update dependencies in pom.xml:

```js
    <dependency>
          <groupId>com.lightbend.lagom</groupId>
          <artifactId>lagom-javadsl-persistence-cassandra_${scala.binary.version}</artifactId>
          <exclusions>
              <exclusion>
                  <groupId>com.datastax.cassandra</groupId>
                  <artifactId>cassandra-driver-core</artifactId>
              </exclusion>
          </exclusions>
      </dependency>

      <!-- https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core -->
      <dependency>
          <groupId>com.datastax.cassandra</groupId>
          <artifactId>cassandra-driver-core</artifactId>
          <version>3.8.0</version>
      </dependency>
```

Next we need to unpack the Astra Secure Bundle and gather some connection details found within:

```js
cassandra-snapshot-store {
  authentication.username = ${cassandra.default.authentication.username}
  authentication.password = ${cassandra.default.authentication.password}
  ssl.truststore.path = ${cassandra.default.ssl.truststore.path}
  ssl.truststore.password = ${cassandra.default.ssl.truststore.password}
  ssl.keystore.path = ${cassandra.default.ssl.keystore.path}
  ssl.keystore.password = ${cassandra.default.ssl.keystore.password}
  keyspace = ${package.cassandra.keyspace}
  keyspace-autocreate = true
  tables-autocreate = true
}
```

{% include astra_help.html %}