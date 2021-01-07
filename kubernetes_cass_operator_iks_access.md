---
layout: default
title: Cass-Operator + IKS Pod Access
description: How to access Cassandra Pods
permalink: /kubernetes/cass-operator/iks/access/
---

# How To: Access IKS Cassandra PODS Terminal

These are my raw working notes, take a look at how record the information I need to answer questions I ask myself while learning Kubernetes.  With good notes I can then easily copy paste the information and quickyl translate to markdown.

Links I used:
1.	[https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/)
2.	[https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec)

I can execute some commands to get Pod Names:

```js
 ~ % ibmcloud ks worker ls --cluster mycluster-dal12-b3c.4x16
OK
ID                                                       Public IP      Private IP      Flavor               State    Status   Zone    Version   
kube-bvhjafed0aiosshvvo8g-myclusterda-default-000001bc   169.47.64.82   10.185.96.183   b3c.4x16.encrypted   normal   Ready    dal12   1.18.13_1536   
kube-bvhjafed0aiosshvvo8g-myclusterda-default-00000259   169.47.64.84   10.185.96.165   b3c.4x16.encrypted   normal   Ready    dal12   1.18.13_1536   
kube-bvhjafed0aiosshvvo8g-myclusterda-default-000003a0   169.47.64.86   10.185.96.186   b3c.4x16.encrypted   normal   Ready    dal12   1.18.13_1536 
```

```js
 ~ % kubectl -n cass-operator get pods                                                                                        
NAME                             READY   STATUS    RESTARTS   AGE
cass-operator-557f6689df-qftdf   1/1     Running   0          14m
cluster1-dc1-default-sts-0       2/2     Running   0          11m
cluster1-dc1-default-sts-1       2/2     Running   0          11m
cluster1-dc1-default-sts-2       2/2     Running   0          11m
```

```js
 ~ % kubectl -n cass-operator get pod cluster1-dc1-default-sts-0                                                         
NAME                         READY   STATUS    RESTARTS   AGE
cluster1-dc1-default-sts-0   2/2     Running   0          12m
```

I can use bash on the pod:

```js
 ~ % kubectl -n cass-operator exec --stdin cluster1-dc1-default-sts-0 -- /bin/bash
Defaulting container name to cassandra.
Use 'kubectl describe pod/cluster1-dc1-default-sts-0 -n cass-operator' to see all of the containers in this pod.
```
![WARNING](/assets/images/error.png){:height="24" width="24" align="absmiddle" style="padding: 10px"}  the bash commands are limited<br>
![WARNING](/assets/images/error.png){:height="24" width="24" align="absmiddle" style="padding: 10px"}  I can see the file system and find /config/cassandra.yaml but cannot edit

I can run nodetool status:

```js
Datacenter: dc1
===============
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving/Stopped
--  Address         Load       Tokens       Owns (effective)  Host ID                               Rack
UN  172.30.67.15    165.11 KiB  1            100.0%            49923960-3bb4-4aae-ae6e-19d1a9ff7809  default
UN  172.30.163.68   143.49 KiB  1            100.0%            133c50a9-df68-481b-8344-6a63140d5e57  default
UN  172.30.234.133  111.8 KiB  1            100.0%            e1ce2f35-90cb-4594-aa3b-b2ee2ed47860  default
```

I can run dsetool:

```js
dsetool insights_config --show_config
{
  "mode" : "ENABLED_WITH_LOCAL_STORAGE",
  "config_refresh_interval_in_seconds" : 30,
  "metric_sampling_interval_in_seconds" : 30,
  "data_dir_max_size_in_mb" : 1024,
  "node_system_info_report_period" : "PT1H"
}

dsetool insights_filters
[simple commands]:
  --show_filters
  --remove_all_filters
[filtering actions]:
  --add
  --remove
  --allow
  --deny
  --global
  --insights_only
 <regex>

[examples]:
 insights_filters --add --global --deny .*KeyspaceMetrics.*
 insights_filters --add --insights_only --deny .*gc.*
 insights_filters --remove_all_filters
```

I can find metrics and log locations:

```js
cd /var/lib/cassandra/insights_data
ls
insights

cd /var/log/cassandra
ls
audit
debug.log
dse-collectd.log
dse-collectd.pid
gc.log.0.current
gremlin.log
system.log
```

# What's Next?

Stay tuned to the [Cass-Operator + IKS](/kubernetes/cass-operator/iks/) page as I complete exposing how to use the [Datastax Cassandra Operator for Kubernetes](https://docs.datastax.com/en/cass-operator/doc/cass-operator/cassOperatorTOC.html) on [IBM Cloud IKS Platform](https://cloud.ibm.com/kubernetes/).

{% include kubernetes_help.html %}