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
ibmcloud login -u <username> -p <password> -a cloud.ibm.com -r us-south -g Default
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

*   IBM IKS documentation is vast volumes of information
*   Major functional system differences based on before or after Dec 1, 2020
*   The DataStax Kubernetes Operator for Apache Cassandra is BETA
*   The differences between documentation (GKE) and IKS are related to storage and networking
*   More major differences between Classic and VPC IKS Clusters

# What Are Lessons Learned?

*   I had to learn how to connect my local environment to IBM Cloud and IKS using IBM Cloud SDK.
*   I had to learn to navigate the vast volumes of text at [IBM Cloud Docs](https://cloud.ibm.com/docs).   
*   I needed to utilize the [IBM Cloud Slack Community](https://join.slack.com/t/ibm-cloud-success/shared_invite/zt-ki1gmwe9-M0bFaI_nMSAF1RzDMqw_wg) help to resolve some friction.

# Cass-Operator + IKS How Tos

<p>&nbsp;</p>

<div class="mui-container">
  <div class="home mui-row">
      <div class="mui-col-md-2">
        <article class="mui-panel">
          <div class="bg-holder bg-deep-purple" style="background:url(/assets/images/database.jpg) no-repeat center center">
            <span class="post-list-title">
              <a class="post-link" href="/kubernetes/cass-operator/iks/access/">IKS Pod Access</a>
            </span>
            <div class="img-overlay"></div>
            <a href="/kubernetes/cass-operator/iks/access/" class="overlay"></a>
          </div>
          <div class="post-data">
            <p class="post-excerpt">Accessing cassandra pods with terminal</p>
            <p><b>tags:</b> cassandra, kubernetes, k8s, pods, kubectl, bash</p>
          </div>
        </article>
      </div>
      <div class="mui-col-md-2">
        <article class="mui-panel">
          <div class="bg-holder bg-deep-purple" style="background:url(/assets/images/database.jpg) no-repeat center center">
            <span class="post-list-title">
              <a class="post-link" href="/kubernetes/cass-operator/iks/backups/">IKS Cass Backups</a>
            </span>
            <div class="img-overlay"></div>
            <a href="/kubernetes/cass-operator/iks/backups/" class="overlay"></a>
          </div>
          <div class="post-data">
            <p class="post-excerpt">Backing up cassandra data on IKS</p>
            <p><b>tags:</b> backup, cassandra, iks, ibm, cloud, storage, s3</p>
          </div>
        </article>
      </div>
      <div class="mui-col-md-2">
        <article class="mui-panel">
          <div class="bg-holder bg-deep-purple" style="background:url(/assets/images/database.jpg) no-repeat center center">
            <span class="post-list-title">
              <a class="post-link" href="/kubernetes/cass-operator/iks/metrics/">IKS Cass Metrics</a>
            </span>
            <div class="img-overlay"></div>
            <a href="/kubernetes/cass-operator/iks/metrics/" class="overlay"></a>
          </div>
          <div class="post-data">
            <p class="post-excerpt">Prometheus & Graphana Metrics</p>
            <p><b>tags:</b> grafana, prometheus, iks, cassandra, metrics</p>
          </div>
        </article>
      </div>
  </div>  
</div>

# What's Next?

Stay tuned for more updates here and additional IKS cass operator topics as I dig in even more with the Datastax Cassandra Operator on IKS.  I am currently working on current content updates and new Ingress sections to be posted soon.  Additionally my lessons learned and the required differences for IKS will be used to contribue to the Cassandra Operator Knowledge base.  Later we should see the Cass Operator documentation updated with IKS as a supported platform.  

{% include kubernetes_help.html %}