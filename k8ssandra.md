---
layout: default
title: K8ssandra
description: A blog all about K8ssandra
permalink: /k8ssandra/
---

# What is K8ssandra?
<br>

<b><i>Kubernetes + Cassandra = k8ssandra</i></b>


For now do some reasearch:

![Branching](/assets/images/k8ssandra-horizontal.jpg)

| [Website](https://k8ssandra.io/)        | [Community](https://forum.k8ssandra.io/)        | [Github Repo](https://github.com/k8ssandra)          | [Docs](https://docs.k8ssandra.io/) |

<p>&nbsp;</p>

# Check out my k8ssandra MemeGen Project

I have been working on Meme Generator demos with a partner and this latest one is a full k8ssandra platform running the app with cassandra 4.0 backend.  In order to provide a backend interface for this web app I built a simple /api/ with php.  The /api/ originated from [jeromatron](https://github.com/jeromatron) repo [astra-php-bank](https://github.com/jeromatron/astra-php-bank) which is a sample of php auth mechanism and curl to communicate with astra.  I dev'd this on Astra for awhile before migrating it to Cassandra 4.0 + Stargate in k8ssandra.  Stay tuned here as this becomes a more official project and associated assets.

<div class="mui-container">
    <div class="home mui-row">
      <div class="mui-col-md-6">
        <article class="mui-panel">
          <div class="bg-holder bg-deep-purple" style="background:url(/assets/images/database.jpg) no-repeat center center">
            <span class="post-list-title">
              <a class="post-link" href="https://github.com/ds-steven-matison/MemesGen">GitHub: MemeGen</a>
            </span>
            <div class="img-overlay"></div>
            <a href="https://github.com/ds-steven-matison/MemesGen" class="overlay"></a>
          </div>
          <div class="post-data">
            <p class="post-excerpt">My GitHub repo for the SADA + Datastax Meme Generator demo.</p>
            GitHub Repo: <a href="https://github.com/ds-steven-matison/MemesGen">MemesGen</a>
          </div>
        </article>
    </div>
  </div>
</div>

# k8ssandra How Tos and More

I will use this page to make small articles and as I build them out will break them up into sub pages similar to other blog sections.  For now I am going to just drop interesting content sections in here that some of my guys have helped me resolve.  It was quite a fun task going from the documented k8ssandra cluster and external docker app to having everything inside the k8ssandra cluster and fully automated role out.   

# How do you list the k8s pod environment variables?

While working on my app I needed a way to ship environment variables from k8ssandra environment to the applicaton.  This is done by mapping the env object in the deployment.yaml as such:

```js
        env:
          - name: KEYSPACE
            value: "memegen2"
          - name: K8S_USERNAME
            valueFrom:
              secretKeyRef:
                name: k8ssandra-superuser 
                key: username
          - name: K8S_PASSWORD
            valueFrom: 
              secretKeyRef:
                name: k8ssandra-superuser 
                key: password               
          - name: K8S_AUTH_URL
            value: "http://k8ssandra-dc1-stargate-service:8081/v1/auth"
          - name: STARGATE_URL
            value: "http://k8ssandra-dc1-stargate-service:8082"
          - name: GRAPHQL_URL
            value: "http://k8ssandra-dc1-stargate-service:8080/graphql/"
```
:bulb: Notice there is no instance of a password, ip address, or other idenfiable parameters exposed.


With the deployment sorted I needed to verify the environment variables were available in my pod.  I did this by executing printenv as such and making sure my values exist:

```js
kubectl exec -it memegen-v2-545d9d5474-b6qp6 -- sh 
# printenv

KUBERNETES_PORT=tcp://10.43.0.1:443
K8SSANDRA_KUBE_PROMETHEUS_PROMETHEUS_SERVICE_HOST=10.43.79.223
KUBERNETES_SERVICE_PORT=443
K8SSANDRA_REAPER_REAPER_SERVICE_PORT_8080_TCP_PORT=8080
K8SSANDRA_DC1_STARGATE_SERVICE_SERVICE_PORT_HEALTH=8084
K8SSANDRA_REAPER_REAPER_SERVICE_PORT_8080_TCP_PROTO=tcp
KEYSPACE=memegen2
K8SSANDRA_KUBE_PROMETHEUS_PROMETHEUS_PORT_9090_TCP_ADDR=10.43.79.223
HOSTNAME=memegen-v2-545d9d5474-b6qp6
K8SSANDRA_KUBE_PROMETHEUS_OPERATOR_PORT=tcp://10.43.220.53:443
K8SSANDRA_KUBE_PROMETHEUS_OPERATOR_SERVICE_PORT=443
K8SSANDRA_REAPER_REAPER_SERVICE_SERVICE_PORT=8080
K8SSANDRA_REAPER_REAPER_SERVICE_PORT=tcp://10.43.235.8:8080
K8SSANDRA_KUBE_PROMETHEUS_PROMETHEUS_PORT_9090_TCP_PORT=9090
HOME=/root
STARGATE_URL=http://k8ssandra-dc1-stargate-service:8082
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_9042_TCP_ADDR=10.43.22.217
K8SSANDRA_KUBE_PROMETHEUS_PROMETHEUS_PORT_9090_TCP_PROTO=tcp
CASS_OPERATOR_METRICS_SERVICE_PORT_HTTP_METRICS=8383
K8S_AUTH_URL=http://k8ssandra-dc1-stargate-service:8081/v1/auth
K8SSANDRA_DC1_STARGATE_SERVICE_SERVICE_HOST=10.43.22.217
K8SSANDRA_KUBE_PROMETHEUS_PROMETHEUS_SERVICE_PORT=9090
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_8080_TCP_ADDR=10.43.22.217
CASS_OPERATOR_METRICS_SERVICE_HOST=10.43.140.23
K8SSANDRA_KUBE_PROMETHEUS_PROMETHEUS_PORT=tcp://10.43.79.223:9090
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_8081_TCP_ADDR=10.43.22.217
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_8082_TCP_ADDR=10.43.22.217
K8SSANDRA_REAPER_REAPER_SERVICE_PORT_8080_TCP=tcp://10.43.235.8:8080
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_9042_TCP_PORT=9042
K8S_USERNAME=k8ssandra-superuser
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_9042_TCP_PROTO=tcp
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_8080_TCP_PORT=8080
GRAPHQL_URL=http://k8ssandra-dc1-stargate-service:8080/graphql/
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_8081_TCP_PORT=8081
K8SSANDRA_GRAFANA_SERVICE_HOST=10.43.100.226
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_8084_TCP_ADDR=10.43.22.217
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_8080_TCP_PROTO=tcp
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_8085_TCP_ADDR=10.43.22.217
K8SSANDRA_KUBE_PROMETHEUS_PROMETHEUS_SERVICE_PORT_WEB=9090
K8SSANDRA_DC1_STARGATE_SERVICE_SERVICE_PORT_GRAPHQL=8080
K8SSANDRA_REAPER_REAPER_SERVICE_SERVICE_PORT_APP=8080
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_8081_TCP_PROTO=tcp
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_8082_TCP_PORT=8082
CASS_OPERATOR_METRICS_PORT_8383_TCP_ADDR=10.43.140.23
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_8082_TCP_PROTO=tcp
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_8084_TCP_PORT=8084
CASS_OPERATOR_METRICS_PORT=tcp://10.43.140.23:8383
K8SSANDRA_DC1_STARGATE_SERVICE_PORT=tcp://10.43.22.217:8080
K8SSANDRA_KUBE_PROMETHEUS_PROMETHEUS_PORT_9090_TCP=tcp://10.43.79.223:9090
CASS_OPERATOR_METRICS_SERVICE_PORT=8383
K8SSANDRA_DC1_STARGATE_SERVICE_SERVICE_PORT=8080
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_8085_TCP_PORT=8085
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_8084_TCP_PROTO=tcp
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_8085_TCP_PROTO=tcp
CASS_OPERATOR_METRICS_PORT_8383_TCP_PORT=8383
CASS_OPERATOR_METRICS_PORT_8383_TCP_PROTO=tcp
TERM=xterm
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_9042_TCP=tcp://10.43.22.217:9042
K8SSANDRA_GRAFANA_PORT=tcp://10.43.100.226:80
K8SSANDRA_GRAFANA_SERVICE_PORT=80
K8SSANDRA_GRAFANA_SERVICE_PORT_SERVICE=80
KUBERNETES_PORT_443_TCP_ADDR=10.43.0.1
CASS_OPERATOR_METRICS_PORT_8686_TCP_ADDR=10.43.140.23
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_8080_TCP=tcp://10.43.22.217:8080
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
K8SSANDRA_DC1_STARGATE_SERVICE_SERVICE_PORT_METRICS=8085
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_8081_TCP=tcp://10.43.22.217:8081
K8SSANDRA_KUBE_PROMETHEUS_OPERATOR_PORT_443_TCP_ADDR=10.43.220.53
KUBERNETES_PORT_443_TCP_PORT=443
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_8082_TCP=tcp://10.43.22.217:8082
CASS_OPERATOR_METRICS_PORT_8686_TCP_PORT=8686
KUBERNETES_PORT_443_TCP_PROTO=tcp
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_8084_TCP=tcp://10.43.22.217:8084
CASS_OPERATOR_METRICS_PORT_8686_TCP_PROTO=tcp
K8SSANDRA_DC1_STARGATE_SERVICE_SERVICE_PORT_CASSANDRA=9042
K8SSANDRA_KUBE_PROMETHEUS_OPERATOR_PORT_443_TCP_PORT=443
K8SSANDRA_GRAFANA_PORT_80_TCP_ADDR=10.43.100.226
K8SSANDRA_DC1_STARGATE_SERVICE_PORT_8085_TCP=tcp://10.43.22.217:8085
DEBIAN_FRONTEND=noninteractive
CASS_OPERATOR_METRICS_PORT_8383_TCP=tcp://10.43.140.23:8383
K8SSANDRA_KUBE_PROMETHEUS_OPERATOR_PORT_443_TCP_PROTO=tcp
K8SSANDRA_DC1_STARGATE_SERVICE_SERVICE_PORT_AUTHORIZATION=8081
K8SSANDRA_GRAFANA_PORT_80_TCP_PORT=80
K8SSANDRA_GRAFANA_PORT_80_TCP_PROTO=tcp
K8S_PASSWORD=OydXi6iO4MEUUqODGAC3
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT_443_TCP=tcp://10.43.0.1:443
CASS_OPERATOR_METRICS_SERVICE_PORT_CR_METRICS=8686
CASS_OPERATOR_METRICS_PORT_8686_TCP=tcp://10.43.140.23:8686
KUBERNETES_SERVICE_HOST=10.43.0.1
PWD=/
K8SSANDRA_KUBE_PROMETHEUS_OPERATOR_SERVICE_PORT_HTTPS=443
K8SSANDRA_KUBE_PROMETHEUS_OPERATOR_PORT_443_TCP=tcp://10.43.220.53:443
K8SSANDRA_KUBE_PROMETHEUS_OPERATOR_SERVICE_HOST=10.43.220.53
K8SSANDRA_REAPER_REAPER_SERVICE_PORT_8080_TCP_ADDR=10.43.235.8
K8SSANDRA_REAPER_REAPER_SERVICE_SERVICE_HOST=10.43.235.8
K8SSANDRA_GRAFANA_PORT_80_TCP=tcp://10.43.100.226:80
K8SSANDRA_DC1_STARGATE_SERVICE_SERVICE_PORT_REST=8082
```

:warning:  I left this full list so that I can come back later and begin to use this env variables where they can be useful.

# What's Next?

Stay tuned here as I will be writing short articles as I finish this project, use k8ssandra for other projects, and continue to learn both kubernetes and k8ssandra.

{% include kubernetes_help.html %}