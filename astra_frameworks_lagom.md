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
stevenmatison@smatison-rmbp16 secure-connect-12413de1-f1a2-4171-9dfb-7df93b64cb55 % ls -al          
total 64
drwxr-xr-x@  10 stevenmatison  staff    320 Nov 24 09:25 .
drwx------@ 431 stevenmatison  staff  13792 Mar 19 08:55 ..
-rw-------@   1 stevenmatison  staff   1460 Nov 24 08:34 ca.crt
-rw-------@   1 stevenmatison  staff   1464 Nov 24 08:34 cert
-rw-r--r--@   1 stevenmatison  staff   3719 Nov 24 08:34 cert.pfx
-rw-r--r--@   1 stevenmatison  staff    476 Nov 24 08:34 config.json
-rw-r--r--@   1 stevenmatison  staff    193 Nov 24 08:34 cqlshrc
-rw-r--r--@   1 stevenmatison  staff   2812 Nov 24 08:34 identity.jks
-rw-------@   1 stevenmatison  staff   1675 Nov 24 08:34 key
-rw-r--r--@   1 stevenmatison  staff   1522 Nov 24 08:34 trustStore.jks

```

Next checkout the contents of config.json:

```js
stevenmatison@smatison-rmbp16 secure-connect-12413de1-f1a2-4171-9dfb-7df93b64cb55 % cat config.json 
{
  "host": "2703f654-cea5-48e5-98d2-373f2a323d5f-us-east1.db.astra.datastax.com",
  "port": 32220,
  "keyspace": "petclinic",
  "localDC": "dc-1",
  "caCertLocation": "./ca.crt",
  "keyLocation": "./key",
  "certLocation": "./cert",
  "keyStoreLocation": "./identity.jks",
  "keyStorePassword": "iT4MjPX78Rcxm9W0n",
  "trustStoreLocation": "./trustStore.jks",
  "trustStorePassword": "4wv2UDpH5CN7yam6M",
  "csvLocation": "./data",
  "pfxCertPassword": "myY9e216n8PTDwbJW"
}

```

Apply values in lagom configs:

```js

cassandra.default {

  contact-points = ${?CASSANDRA_CONTACT_POINTS}

  authentication {
    username = ${?CASSANDRA_USERNAME}
    password = ${?CASSANDRA_PASSWORD}
  }

  ssl {
    truststore.path = ${?CASSANDRA_TRUSTSTORE_PATH}
    truststore.password = ${?CASSANDRA_TRUSTSTORE_PASSWORD}
    keystore.path = ${?CASSANDRA_KEYSTORE_PATH}
    keystore.password = ${?CASSANDRA_KEYSTORE_PASSWORD}
  }
}

//Those values are environment variables
// for example: CASSANDRA_CONTACT_POINTS= http://[host]:[port] where host/port is from config.json above
```


{% include astra_help.html %}