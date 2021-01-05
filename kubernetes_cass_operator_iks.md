---
layout: default
title: Cass-Operator + IKS
description: A blog all about Cass-Operator and IKS
permalink: /kubernetes/cass-operator/iks/
---

{% include under_construction.html %}

# How To: Cass-Operator on IKS

In the previous article I introduced the [Cass Operator](/kubernetes/cass-operator/).  In this article I am going to jump right in feet first and expose how you can complete the documeted steps for GKE but in the IKS Platform.   Now the main point here with a unified system (k8s) is the adminstrative effort should be almost identical across platforms (gke,eks,aks,pks,etc).  Ping me to talk about the differences.

# What is IKS?

IKS is [IBM Cloud's Kubernetes Engine](https://www.ibm.com/cloud/kubernetes-service).  I was recently given the opportunity to test out [Cass-Operator](https://github.com/datastax/cass-operator) on GKE and then had an internal ping to support the Cass Operator on IKS.  Challenge Accepted!   This post is me exposing the steps I had to take to get Cassandra DSE rolled out on IKS.

# Creating Kubernetes Cluster on IKS

The [IBM Cloud UI](https://cloud.ibm.com/) was super easy to use.  I was able to get a Kubernetes Cluster up and running pretty easy and with some free credits.  I was even easily able to get my terminal connected.  I wont go into detail here as you can easily find this information after you create your cluster.   With a cluster created and your environment setup, the rest is almost too easy.

# What Commands Did I Run?

```js
ibmcloud login -u <username> -p <password> -a cloud.ibm.com -r us-south
ibmcloud ks cluster config --cluster mycluster-dal12-b3c.4x16
helm repo add iks-charts https://icr.io/helm/iks-charts
helm repo update
kubectl create -f https://raw.githubusercontent.com/datastax/cass-operator/master/docs/user/cass-operator-manifests-v1.18.yaml
helm install block-storage-plugin iks-charts/ibmcloud-block-storage-plugin -n cass-operator
kubectl create -f https://raw.githubusercontent.com/ds-steven-matison/cass-operator/master/operator/k8s-flavors/iks/storage-block.yaml
kubectl -n cass-operator create -f  https://raw.githubusercontent.com/ds-steven-matison/cass-operator/master/operator/example-cassdc-yaml/dse-6.8.x/example-cassdc-minimal2.yaml
kubectl -n cass-operator get pods
kubectl get secret -o yaml cluster1-superuser -n cass-operator
echo 'password string' | base64 -d
kubectl exec -n cass-operator -i -t -c cassandra cluster1-dc1-default-sts-0 -- cqlsh -u cluster1-superuser -p <password>
```

# What Were The Sharp Edges?

*   IBM IKS documentation is vast volumes of text
*   Major functional system differences based on before or after Dec 1, 2020
*   The DataStax Kubernetes Operator for Apache Cassandra is BETA
*   The differences between documentation (GKE) and IKS are related to storage and networking

# What Are Lessons Learned?

*   I had to learn how to connect my local environment to IBM Cloud and IKS using IBM Cloud sdk (easy).
*   I also had to learn to navigate the vast volumes of text at IBM.   
*   I needed to utilize the IBM Cloud slack community help to resolve some friction.

# How do I access the PODS terminal?

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

# How To: Install Prometheus?

 Prometheus is a free software application used for event monitoring and alerting. It records real-time metrics in a time series database built using a HTTP pull model, with flexible queries and real-time alerting.  You can find more information at [https://prometheus.io/](https://prometheus.io/).

## Prometheus Method 1

The first method I used to install Prometheus on IBM IKS is an post I found by Bibin Wilson.  You can read How to Setup Prometheus Monitoring On Kubernetes Cluster here:
*  [https://devopscube.com/setup-prometheus-monitoring-on-kubernetes/](https://devopscube.com/setup-prometheus-monitoring-on-kubernetes/)

Sources of files used in commands:
*  [https://raw.githubusercontent.com/bibinwilson/kubernetes-prometheus/master/clusterRole.yaml](https://raw.githubusercontent.com/bibinwilson/kubernetes-prometheus/master/clusterRole.yaml)
*  [https://raw.githubusercontent.com/bibinwilson/kubernetes-prometheus/master/config-map.yaml]([https://raw.githubusercontent.com/bibinwilson/kubernetes-prometheus/master/config-map.yaml)

The prometheus-deployment.yaml:

```js
apiVersion: apps/v1
kind: Deployment
metadata:
  name: prometheus-deployment
  namespace: monitoring
  labels:
    app: prometheus-server
spec:
  replicas: 1
  selector:
    matchLabels:
      app: prometheus-server
  template:
    metadata:
      labels:
        app: prometheus-server
    spec:
      containers:
        - name: prometheus
          image: prom/prometheus
          args:
            - "--config.file=/etc/prometheus/prometheus.yml"
            - "--storage.tsdb.path=/prometheus/"
          ports:
            - containerPort: 9090
          volumeMounts:
            - name: prometheus-config-volume
              mountPath: /etc/prometheus/
            - name: prometheus-storage-volume
              mountPath: /prometheus/
      volumes:
        - name: prometheus-config-volume
          configMap:
            defaultMode: 420
            name: prometheus-server-conf
  
        - name: prometheus-storage-volume
          emptyDir: {}
```

The prometheus-service.yaml:

```js
apiVersion: v1
kind: Service
metadata:
  name: prometheus-service
  namespace: monitoring
  annotations:
      prometheus.io/scrape: 'true'
      prometheus.io/port:   '9090'
spec:
  selector: 
    app: prometheus-server
  type: NodePort  
  ports:
    - port: 8080
      targetPort: 9090 
      nodePort: 30000
```

The terminal commands I ran:

```js
kubectl create -f clusterRole.yaml
nano config-map.yaml
kubectl create -f config-map.yaml
kubectl create namespace monitoring
kubectl create -f config-map.yaml
nano prometheus-deployment.yaml
kubectl create -f prometheus-deployment.yaml
kubectl get deployments --namespace=monitoring
kubectl get pods --namespace=monitoring
nano prometheus-service.yaml
kubectl create -f prometheus-service.yaml --namespace=monitoring
```

Port fwd localhost:

```js
kubectl port-forward prometheus-deployment-54686956bd-nhz2s 8080:9090 -n monitoring
```

Then this Prometheus UI works:

	http://localhost:8080/graph?g0.expr=&g0.tab=1&g0.stacked=0&g0.range_input=1h

Also the UI is exposed on public worker IP:

	http://169.47.64.82:30000/graph?g0.expr=&g0.tab=1&g0.stacked=0&g0.range_input=1h

## Prometheus Method 2

The second method I used to install Prometheus on IBM IKS is an official documentation post I found on [https://kubernetes.github.io/](https://kubernetes.github.io/).  You can read more about Prometheus and Grafana installation here:

*  [https://kubernetes.github.io/ingress-nginx/user-guide/monitoring/](https://kubernetes.github.io/ingress-nginx/user-guide/monitoring/)

I completed the helm steps here:

&nbsp; [https://kubernetes.github.io/ingress-nginx/deploy/](https://kubernetes.github.io/ingress-nginx/deploy/)

![WARNING](/assets/images/error.png){:height="24" width="24" align="absmiddle" style="padding: 10px"}  this Ingres installs as default namespace<br>
![WARNING](/assets/images/error.png){:height="24" width="24" align="absmiddle" style="padding: 10px"}  needs to be ingress-nginx

The terminal commands I ran:

```js
kubectl create namespace ingress-nginx
kubectl get svc -n ingress-nginx
kubectl apply --kustomize github.com/kubernetes/ingress-nginx/deploy/prometheus/
helm install my-release ingress-nginx/ingress-nginx
POD_NAME=$(kubectl get pods -l app.kubernetes.io/name=ingress-nginx -o jsonpath='{.items[0].metadata.name}')\
kubectl exec -it $POD_NAME -- /nginx-ingress-controller --version
kubectl get pods --all-namespaces
kubectl get svc -n default
helm install my-release ingress-nginx/ingress-nginx -n ingress-nginx
kubectl apply --kustomize github.com/kubernetes/ingress-nginx/deploy/prometheus/
kubectl get svc -n ingress-nginx
kubectl get nodes -o wide
kubectl apply --kustomize github.com/kubernetes/ingress-nginx/deploy/grafana/
kubectl get svc -n ingress-nginx
```

After install Promethus UI works:

	http://169.47.64.84:32165/graph

Graphana works too:

	http://169.47.64.84:32393/login
	admin / admin


# How To: Backup Cassandra on IKS

Backups in the kubernetes ecosystem is quite a bit different than traditional cassandra administration.  At first glance it may seem like a complicated tasks, but i am going to expose just how easy it is.   My prefered method to move towards a lengthy technical goal is to start with small working technical base, and slowly iterate towards what I need.   In order to do backups on IKS I have to accomplish a few things:

1.  I need to enable backups in my cassandra cluster yaml
2.  I need to create a backup store (location) via CQL
3.  I need to create a backup configuration (timing) via CQL

First,  the documentation here was a little confusing in how to enable backups.  At first look the config is documented as:

```js
  config:
    backup_service:
      enabled: true
```

However I had to get information from internal resource that it is actually:

```js
  config:
    cassandra-yaml:
      backup_service:
        enabled: true
```

With backups enabled in my cluster, I take the path of least resistance and create a backup store on local file system.  I do not want to backup like this, but what i want to do is learn the backup process without fighting cloud storage access, authorization, and configuration.  With the local file system method working I then tackle a backup store on IBMs cloud storage which is basically S3.   The details of both of these are included in next sub sections.

## How do I backup to File System?

  The complete documenation for backups starts [here](https://docs.datastax.com/en/dse/6.8/dse-admin/datastax_enterprise/operations/opsBackupRestoreCreateBackupStore.html).

{% raw %}
![WARNING](/assets/images/error.png){:height="24" width="24" align="absmiddle" style="padding: 10px"}  MAC users be warned, copy/pasting single quoted text often turns to ’quoted’ which will cause an error in cql.
{% endraw %}

First I need a backup store:

```js
CREATE BACKUP STORE store_name
USING 'FSBlobStore' WITH settings = {'path':'/tmp'};
```

Next I need to create a BACKUP CONFIGURATION which sets the timing for the backup using cron expression:

```js
CREATE BACKUP CONFIGURATION config_name 
    OF keyspace
    TO STORE backup_store    
    WITH frequency = 'cron expression'
    AND enabled = true;
```

Before I can do that I need to create the keyspace to backup:

```js
CREATE KEYSPACE IF NOT EXISTS demo WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'dc1' : 3 };
```

For my first test that BACKUP CONFIGURATION and frequency cron statement looks like:

```js
CREATE BACKUP CONFIGURATION config_name 
OF demo
TO STORE store_name   
WITH frequency = '0 17 * * *'
AND enabled = true;
```

Now I am expecting to see some kind of backup at 1pm and I realize the pod time is probably different:

```js  
uptime
  17:13:04 up  4:50,  0 users,  load average: 0.22, 0.30, 0.66
```

![WARNING](/assets/images/error.png){:height="24" width="24" align="absmiddle" style="padding: 10px"}  server time is off

So I drop my first config with this command: 

```js
DROP BACKUP CONFIGURATION config_name;
```

And I redo it with the correct hour:

```js
CREATE BACKUP CONFIGURATION config_name 
OF demo
TO STORE store_name   
WITH frequency = '0 18 * * *'
AND enabled = true;
```

Next I needed to create something to backup: 

```js
CREATE TABLE videos ( 
video_id timeuuid, 
added_date timestamp, 
description text,
title text, 
user_id uuid,
PRIMARY KEY (video_id)
);

INSERT INTO videos (video_id,added_date,description,title,user_id) VALUES (now(),dateof(now()),'description',uuid());
```

And now I wait until the top of the hour, and validate the backup exists by logging into pod with bash:

```js
kubectl -n cass-operator exec --stdin cluster1-dc1-default-sts-0 -- /bin/bash                                                                                       
Defaulting container name to cassandra.
Use 'kubectl describe pod/cluster1-dc1-default-sts-0 -n cass-operator' to see all of the containers in this pod.
uptime
 17:51:07 up  5:28,  0 users,  load average: 0.18, 0.45, 0.58
cd /tmp/dse
ls
dse.EJT1Kqc3C
ls
dse.EJT1Kqc3C
snapshots
cd /tmp/dse/snapshots
ls
15df1d07-c385-4b4c-a673-add1741ebe16

```

I now have a working foundation for moving forward with IBM Cloud Storage

## How do I backup to S3 on IKS?

Coming next...

# What's Next?

Stay tuned for more updates here and additional IKS cass operator topics as I dig in even more with the Datastax Cassandra Operator on IKS.  I am currently working on Backups, Ingress, Metrics sections to be posted soon.  Soon we should see the Cass Operator documentation updated with IKS as a supported platform.  Additionally my lessons learned and the required differences for IKS will be used to contribue to the Cassandra Operator Knowledge base.  

{% include kubernetes_help.html %}