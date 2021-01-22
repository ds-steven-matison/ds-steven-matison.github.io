---
layout: default
title: Cass-Operator
description: A blog all about Cass-Operator
permalink: /kubernetes/cass-operator/
---

{% include under_construction.html %}

# What is the Cass-Operator?

For more information about DataStax Cass Operator go [here](https://docs.datastax.com/en/cass-operator/doc/cass-operator/cassOperatorTOC.html).  Also be sure to check out [Cass-Operator on Github](https://github.com/datastax/cass-operator).

# Where Can I use Cass Operator?

The cass-operator is available on the following Kubernetes Engines:

1.  [GKE](https://cloud.google.com/kubernetes-engine) - Google Kubernetes Engine
2.  [EKS](https://aws.amazon.com/eks/) - Amazon Elastic Kubernetes Service
3.  [AKS](https://azure.microsoft.com/) - Azure Kubernetes Service
4.  [PKS](https://docs.pivotal.io/pks/1-6/index.html) - Pivotal Container Service

I am now currently working on validating, documementing, and deploying on [IKS](https://www.ibm.com/cloud/kubernetes-service) - IBM Cloud Kubernetes Service.  Check out my [How To: Cass-Operator on IKS](/kubernetes/cass-operator/iks/) post for more details.

# What's Next?

Go deeper with the Kubernetes Cassandra Operator with additional cass operator topics below.

<p>&nbsp;</p>

<div class="mui-container">
  <div class="home mui-row">
      <div class="mui-col-md-2">
        <article class="mui-panel">
          <div class="bg-holder bg-deep-purple" style="background:url(/assets/images/database.jpg) no-repeat center center">
            <span class="post-list-title">
              <a class="post-link" href="/kubernetes/cass-operator/iks/">IKS Cass Operator</a>
            </span>
            <div class="img-overlay"></div>
            <a href="/kubernetes/cass-operator/iks/" class="overlay"></a>
          </div>
          <div class="post-data">
            <p class="post-excerpt">A blog all about Cass-Operator and IKS</p>
            <p><b>tags:</b> cassandra, kubernetes, operator, iks, ibmcloud</p>
          </div>
        </article>
      </div>
      <div class="mui-col-md-2">
        <article class="mui-panel">
          <div class="bg-holder bg-deep-purple" style="background:url(/assets/images/database.jpg) no-repeat center center">
            <span class="post-list-title">
              <a class="post-link" href="/kubernetes/cass-operator/kube-prometheus-stack/">Kube Prometheus Stack</a>
            </span>
            <div class="img-overlay"></div>
            <a href="/kubernetes/cass-operator/kube-prometheus-stack/" class="overlay"></a>
          </div>
          <div class="post-data">
            <p class="post-excerpt">How to install kube-prometheus-stack with Helm Charts.</p>
            <p><b>tags:</b> helm, chart, kubernetes, prometheus, grafana</p>
          </div>
        </article>
      </div>
  </div>  
</div>


{% include kubernetes_help.html %}