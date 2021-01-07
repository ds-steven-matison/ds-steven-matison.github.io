---
layout: default
title: Cass-Operator + IKS Metrics
description: Metrics with Cass-Operator and IKS
permalink: /kubernetes/cass-operator/iks/metrics/
---

{% include under_construction.html %}

# How To: Cassandra Metrics on IKS

Cassandra Cluster Metrics for kubernetes clusters uses the Metrics Collector to expose metrics to Graphana and Promethesus.  In this post I am going to expose ways to install grapaha and promethesus via kubernetes.  I am also going to go into detail showing how to  install graphana and prometheus using operators and the Cass-Operator Metrics Documentation found [here](https://docs.datastax.com/en/cass-operator/doc/cass-operator/cassOperatorMetricReporterDashboards.html).

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

# What's Next?

Stay tuned to the [Cass-Operator + IKS](/kubernetes/cass-operator/iks/) page as I complete exposing how to use the [Datastax Cassandra Operator for Kubernetes](https://docs.datastax.com/en/cass-operator/doc/cass-operator/cassOperatorTOC.html) on [IBM Cloud IKS Platform](https://cloud.ibm.com/kubernetes/).

{% include kubernetes_help.html %}