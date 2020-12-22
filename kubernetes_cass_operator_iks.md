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

# What's Next?

My progress here has helped create a new contribution to the cass-operator repo: [PR 344](https://github.com/datastax/cass-operator/pull/344).  Soon we should see the documentation updated with IKS as a supported platform.   Stay tuned for more updates here and additional IKS cass operator topics as I dig in even more with the Datastax Cassandra Operator on IKS.

{% include kubernetes_help.html %}