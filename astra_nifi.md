---
layout: default
title: Astra + NiFi
description: How to leverage Astra with NiFi
permalink: /astra/nifi/
---

{% include under_construction.html %}

# What is NiFi?

If there is anything I love more than Astra, Postman, and Ansible, its [NiFi](http://nifi.apache.org/).  Apache NiFi is a software project from the Apache Software Foundation designed to automate the flow of data between software systems.  It is super powerful tool I have been using for a few years to develope data flows and data pipelines.  With NiFi I can do just about anything without writing a single line of code.   You can use NiFi's invokeHttp processor for any Astra API calls.  You can also use native NiFi Cassandra Processors (QueryCassandraRecord, PutCassandraRecord, and PutCassandraQL) against Astra.

# My Astra NiFi Templates

You can find my official NiFi Astra Cassandra templates [here](https://github.com/ds-steven-matison/NiFi-Templates).  You can also find templates from my previous life [here](https://github.com/steven-matison/NiFi-Templates).  

# How To: Log Ingestion to Astra with NiFi

In this blog I am going to show you how to ingest raw log data into cassandra using NiFi and Astra.   With NiFi ingesting data from any source is super easy.   With Astra and Cassandra ingesting raw data can be a challenge due to data model constraints.  In this demo I am going to remove that constraint and ingest all raw data using Astra/Stargate Document API which is accepting of schemaless JSON data.   Although not a focus off this blog, it is fully possible to build a cassandra data model and do this log ingestion using NiFi Cassandra Processors against standard cassandra database tables.  In this demo we are going to communicate with Astra via Stargate APIs.

## Part One:  Get NiFi Authorized for Astra Calls

![GetAuthToken](/assets/images/get-auth-token.png)

1.  Upload and add [Get Astra Get Auth Token Template](https://github.com/ds-steven-matison/NiFi-Templates/blob/main/Astra_GetAuthToken.xml) to your canvas.  Record the Process Group Id for later.  
2.  Collect Astra details needed: astra databaseid, region, api url, username, password.
3.  Update Process Group variables with API url, process Group Id, Username and Password
4.  Configure and Enable SSL Context Services.  For simple demo purposes we use java cacerts and in my environment I have copied ca certs to local path /nifi/ssl/cacerts.  You will need to locate your path to cacerts and adjust.  The cacerts password is "changeit".  You can also use Astra Secure Bundle and keystore/trustore found within that bundled zip file.
5.  Confirm NiFi host:port in the Blue InvokeHTTP Processors.
6.  Play the data flow and confirm variable astraToken is filled with authorization token.
	
### Things to Note:

*	Top of flow (GenerateFlowFile) will kick off the auth process every 30 minutes.
*	For sake of this demo, all variables are included in GetAuthToken Process Group.  In production or in your data flow you will want those variables in the parent location.  Adjust your own flow accordingly.
*	For demo purposes failure routes are visible.  In production, these may be auto terminated or routed to exception handling.  

## Part Two:  Create Data Flow for Log Ingestion

![ApacheLogFlow](/assets/images/apache_log_flow.png)

In this first example, we are going to ingest apache log data from a custom log file.  Reference the template [Astra Apache Logs to Cassandra with Stargate](https://github.com/ds-steven-matison/NiFi-Templates/blob/main/Astra_Apache_Logs_to_Cassandra_with_Stargate.xml) for this data flow. This log data happens to be on the same NiFi host in the normal /var/log/httpd/ location.   The custom apache log file is in the format of:

{% raw %}
```js
<IfModule log_config_module>
	LogFormat "%>s	%U	%h	%{%Y-%m-%d %H:%M:%S}t" urlsdetails
        CustomLog "/var/log/httpd/access-file-details.log" urlsdetails
</IfModule>
```
{% endraw %}

The CSVReader Schema used in QueryRecord is as follows:

```js
{ "name" : "apache_logs", "type" : "record", 
	
	"fields" : [ 
		{ "name" : "http_status", "type" : "string" }, 
		{ "name" : "access_url", "type" : "string" },
		{ "name" : "ip", "type" : "string" },
		{ "name" : "apachetime", "type" : "string" }
	]
      
}
```

And the output of the JSON Writer is as follows:

```js
{"http_status":"200","access_url":"/INTROV8.mp3","ip":"115.164.45.55","apachetime":"2021-01-26 14:16:58"}
```

:warning: Notice this JSON structure is exactly what we need to insert into Astra. We do not have to create the collection or schema ahead of time.  This collection creation will automatically happen with the delivery of the first document.

### Things to Note:

*	For portability of the log data flow template, the SSL Context Service is duplicated.  You can adjust your flow to use a single context service at the root canvas level.
*	Some of the NiFi Variables from above template are referenced in this template.  Adjust your flow accordingly with root level variables or import this template into same Process Group above.


## Part Three: Verify Log Data In With Datastax Studio

Login to the Astra UI and navigate to your Studio and execute the following query:

```js
select count(*) FROM apache_log;
```
![ApacheLogCount](/assets/images/apache_log_count.png)

:warning: For this demo it is not important to look at the data, only important to verify results are in Astra.  In future updates I will go into Postman and show how to access the data in a meaningful manner.  For now, let us just bask in the glory of being able to ingest log data to cassandra without data modeling. :muscle: :collision: :muscle: 

## What's Next

We can now use Stargate API to query this data source and even search into the JSON Object.  We can have conversations about the raw data, build cassandra data models, and investigate how this log data can be used downtream from cassandra.   Stay tuned as I add other Log Ingestion Use Cases, a UUID Generator, and more Astra NiFi content here.

{% include astra_help.html %}