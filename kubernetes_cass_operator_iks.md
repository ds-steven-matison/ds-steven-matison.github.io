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

These are my raw working notes, take a look at how I ask myself a question, and then record the information I need to answer it.  

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

After install Promethus UI works:

	http://169.47.64.84:32165/graph

Graphana works too:

	http://169.47.64.84:32393/login
	admin / admin

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

# What's Next?

Stay tuned for more updates here and additional IKS cass operator topics as I dig in even more with the Datastax Cassandra Operator on IKS.  I am currently working on Backups, Ingress, Metrics sections to be posted soon.  Soon we should see the Cass Operator documentation updated with IKS as a supported platform.  Additionally my lessons learned and the required differences for IKS will be used to contribue to the Cassandra Operator Knowledge base.  

{% include kubernetes_help.html %}