---
layout: default
title: Kubernetes
description: A blog all about Kubernetes
permalink: /kubernetes/
---

# What is Kubernetes?
<br>

<div class="mui-container">
  <div class="home mui-row">
      <div class="mui-col-md-2">
        <article class="mui-panel">
          <div class="bg-holder bg-deep-purple" style="background:url(/assets/images/database.jpg) no-repeat center center">
            <span class="post-list-title">
              <a class="post-link" href="/k8ssandra/">K8ssandra</a>
            </span>
            <div class="img-overlay"></div>
            <a href="/k8ssandra/" class="overlay"></a>
          </div>
          <div class="post-data">
            <p class="post-excerpt">Apache Cassandra Kubernetes Platform</p>
            <p><b>tags:</b> apache, cassandra, kubernetes</p>
          </div>
        </article>
      </div>
      <div class="mui-col-md-2">
        <article class="mui-panel">
          <div class="bg-holder bg-deep-purple" style="background:url(/assets/images/database.jpg) no-repeat center center">
            <span class="post-list-title">
              <a class="post-link" href="/kubernetes/cass-operator/">Cass-Operator</a>
            </span>
            <div class="img-overlay"></div>
            <a href="/kubernetes/cass-operator/" class="overlay"></a>
          </div>
          <div class="post-data">
            <p class="post-excerpt">Cass-Operator</p>
            <p><b>tags:</b> kubernetes, cassandra, operator, opensource</p>
          </div>
        </article>
      </div>
      <div class="mui-col-md-2">
        <article class="mui-panel">
          <div class="bg-holder bg-deep-purple" style="background:url(/assets/images/database.jpg) no-repeat center center">
            <span class="post-list-title">
              <a class="post-link" href="/kubernetes/kubectl/">Kubectl</a>
            </span>
            <div class="img-overlay"></div>
            <a href="/kubernetes/kubectl/" class="overlay"></a>
          </div>
          <div class="post-data">
            <p class="post-excerpt">Kubectl</p>
            <p><b>tags:</b> sample, kubectl, commands</p>
          </div>
        </article>
      </div>
    </div>     
</div>

# How To: Tail Multiple Pod Logs in Kubernetes?

I had a scenario where I needed to monitor log output from multiple pods in my kubernetes cluster.  This tool allowed me to do just that. Thank you [dougwettlaufer](https://github.com/dougwettlaufer) for sharing Stern.

<div class="mui-container">
    <div class="home mui-row">
      <div class="mui-col-md-6">
        <article class="mui-panel">
          <div class="bg-holder bg-deep-purple" style="background:url(/assets/images/database.jpg) no-repeat center center">
            <span class="post-list-title">
              <a class="post-link" href="https://github.com/wercker/stern">GitHub: Stern</a>
            </span>
            <div class="img-overlay"></div>
            <a href="https://github.com/wercker/stern" class="overlay"></a>
          </div>
          <div class="post-data">
            <p class="post-excerpt">Multi pod and container log tailing for Kubernetes</p>
            GitHub Repo: <a href="https://github.com/wercker/stern">Stern</a>
          </div>
        </article>
    </div>
  </div>
</div>

{% include kubernetes_help.html %}