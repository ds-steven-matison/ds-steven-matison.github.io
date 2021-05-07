---
layout: default
title: Astra + DSBulk
description: How to use DSBulk with Astra
permalink: /astra/dsbulk/
---

# What is DSBulk?

DSBulk is an amazing tool we use here at DataStax to both unload and load data from Cassandra.  I recently had the opportunity to work with a very large customer to evaluate Astra.  We used DSBulk to unload from On-Prem DSE and load to Astra on GCP.  You can find official information for Astra DSBulk [here](https://docs.datastax.com/en/astra/docs/loading-and-unloading-data-with-datastax-bulk-loader.html).

# How Do You Use DSBulk With Astra?

The first thing we need to do is learn the dsbulk unload command:

```js
dsbulk unload -url [outputfile].csv -k [keyspace] -t [table] -b "~/Downloads/secure-connect-bundle.zip" -u [clientID] -p [clientSecret]
```
 :bulb: This example is for unload from Astra, to unload from another cluster, just include -u, -p, and -h (host), -port, and remove -b (bundle). The official dsbulk manual is found [here](https://docs.datastax.com/en/dsbulk/doc/dsbulk/reference/dsbulkCmd.html).

Next we need to learn the dsbulk load command:

```js
dsbulk load -url [outputfile].csv -k [keyspace] -t [table] -b "~/Downloads/secure-connect-bundle.zip" -u [clientID] -p [clientSecret] -header true
```

Now that we have a working example of each, lets create a script which will iterate through a list of tables and execute each command.

```js
#!/bin/ksh
for table in $(cat table.list)
do
echo "##### working on $table ######"
sudo -E  ./dsbulk-1.8.0/bin/dsbulk load -url dump/${table}.csv -k [keyspace] -t ${table} -b ~/secure-connect-bundle.zip  -u [clientID] -p [clientSecret] -header true
done
```
:warning: This is a sample load, unload will require only slight modifications.

# What Does My Loading Environment Look Like

In order to speed up the process of loading data I uploaded a tar.gz of the unloaded csv to a GCP VM (4vcpu and 16gb RAM) which is more local to my us-east1 Astra Cluster.  With this VM setup these are all of my terminal commands to prepare the server.

```js
sudo apt install python python-pip
sudo apt install rsync
sudo apt-get update
sudo apt-get install software-properties-common
sudo apt install software-properties-common
sudo dpkg --configure -a
sudo apt install software-properties-common
sudo apt-add-repository 'deb http://security.debian.org/debian-security stretch/updates main'
sudo apt-get update
sudo apt-get install openjdk-8-jdk
```
 :bulb: I had my source data, and dsbulk in my local env a quick rysnc gets that to my VM:

```js
rsync -e ssh -azvp dump.tar.gz stevenmatison@[GCPVMIP]:
rsync -e ssh -azvp dsbulk-1.8.0.tar.gz stevenmatison@[GCPVMIP]:
 ```
# What Are The Lessons Learned?

1.  Some of our tables had no records and reported back an error:
```js
At least 1 record does not match the provided schema.mapping or schema.query. Please check that the connector configuration and the schema configuration are correct.
````
2.  Some tables needed the following value added for large column sizes: 
```js
 --connector.csv.maxCharsPerColumn 10000000
 ```
3.  Some tables needed a default value argument: 
```js
--connector.csv.nullValue null
```
4.	DSBulk is an amazing tool with very powerful set of features!!

# What's Next?

Now that I have all my DSE tables loaded to Astra we can begin to test our application against Astra.  If you are looking for more reference examples for DSBulk check out [msmygit's](https://github.com/msmygit) repo for a much deeper dive into [DSBulk](https://github.com/msmygit/dse-titbits/blob/master/documents/datastax_bulk_loader.md). 

{% include astra_help.html %}