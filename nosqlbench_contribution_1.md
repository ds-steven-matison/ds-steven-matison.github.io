---
layout: default
title: NoSQLBench Contribution 1
description: Add Graph Driver to NoSqlBench
permalink: /nosqlbench/contribution/1/
---

# Migrating DSE Graph Module

I was able to make my first major contribution to the NoSQLBench project by migrating legacy dsbench or ebdse DSEGraph testing feature to NoSQLBench.  Take a look at the PR request [here](https://github.com/nosqlbench/nosqlbench/pull/227).  You can find more information about this latest release [here](https://github.com/nosqlbench/nosqlbench/releases/tag/nosqlbench-3.12.155).
For more information about DSEGraph acvity type see the documentation [here](http://docs.nosqlbench.io/#/docs/drivers/dsegraph.md).

Before I was able to start on this migration I needed to learn the basics of both ebdse and nosqlbench.  First I setup a development environment for testing the graph functionality in the original project.  Next I setup nosqlbench so that I could build and test existing functionality.   For testing both projects I used the sample workload for cql-iot.  To validate the DSEGraph driver is installed I use the --list-drivers command on both projects.   When "dsegraph" driver appears I know the module is installed.  

Before Migration:
```
java -jar nb.jar --list-drivers          
cql
cqlverify
diag
http
jmx
kafkaproducer
mongodb
stdout
tcpclient
tcpserver
webdriver
```

After Migration:
```
java -jar nb.jar --list-drivers  
cql
cqlverify
diag
dsegraph
http
jmx
kafkaproducer
mongodb
stdout
tcpclient
tcpserver
webdriver
```


## NoSQLBench Graph Test  

In order to test NoSQlBench with DSE Graph spin up a docker with -g argument:

```js
docker pull datastax/dse-server:6.7.12
docker run -e DS_LICENSE=accept --name dse -p 9042:9042 -d datastax/dse-server:6.7.12 -g
```

Below are some sample graph testing commands.  You will need to install nosqlbench and navigate to /nosqlbench/nb/target.  I am a mac user so I am running the jar directly.  See NoSQLBench [QuickStart](http://docs.nosqlbench.io/index.html#/docs/getting_started) for more information about getting started.

```js
java -jar nb.jar --list-drivers
```
Look for "dsegraph" in the driver list.  If you do not see it, you must have a version before 3.12.155.  Start again and be sure to get the latest release which includes dsegraph.

```js
java -jar nb.jar run type=dsegraph yaml=/nosqlbench/driver-dsegraph-shaded/target/classes/activities/baselines/graph-wheels.yaml tags=phase:create-graph
java -jar nb.jar run type=dsegraph yaml=/nosqlbench/driver-dsegraph-shaded/target/classes/activities/baselines/graph-wheels.yaml graphname=graph_wheels tags=phase:graph-schema
java -jar nb.jar run type=dsegraph yaml=/nosqlbench/driver-dsegraph-shaded/target/classes/activities/baselines/graph-wheels.yaml graphname=graph_wheels tags=name:main-add cycles=100000
```

To validate the graph data created from the test, first open cqlsh against your docker container:
```js
docker exec -it dse cqlsh

cqlsh> use graph_wheels;
cqlsh:graph_wheels> describe keyspace graph_wheels;
```

Next write some select queries against the tables above:

```js
select count(*) from graph_wheels.session_p;
select count(*) from graph_wheels.id_allocation;
select count(*) from graph_wheels.device_e;
select count(*) from graph_wheels.session_e;
select count(*) from graph_wheels.device_p;
```