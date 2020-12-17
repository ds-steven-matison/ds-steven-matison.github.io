---
layout: default
title: Astra Service Broker
description: How use Astra Service Broker
permalink: /astra/service/broker/
---

# How To: Astra Service Broker

Following Christopher Bradfords blog post over at Datastax: [Announcing the Astra Service Broker: Tradeoff-Free Cassandra in Kubernetes](https://www.datastax.com/blog/2020/11/announcing-astra-service-broker-tradeoff-free-cassandra-kubernetes) I was able to create a database on Astra in 6 commands using kubernetes and service account json object for my astra account. I had some challenges along the way but learned some great stuff today.  Check out the setup requirements, create cluster command flow, and the 3 source documents below.

## Environment Setup Requirements
```js
brew install k3d
brew install kubernetes-service-catalog-client
brew install helm
helm repo add svc-cat https://svc-catalog-charts.storage.googleapis.com
helm repo update
```

## Create Cluster & Get Access Credentials
```js
k3d cluster create
helm install catalog svc-cat/catalog --namespace catalog --create-namespace
kubectl create secret generic astra-creds --from-literal=username=unused --from-literal=password=`echo '[Astra Service Account Credential JSON]'| base64`
kubectl create -f astra-service-broker.yaml
kubectl apply -f astra-service-instance.yaml
kubectl apply -f astra-service-binding.yaml
kubectl get secrets devdb -o yaml
```

## Contents of astra-service-broker.yaml

```js
apiVersion: servicecatalog.k8s.io/v1beta1
kind: ServiceBroker
metadata:
  name: astra
spec:
  authInfo:
    basic:
      secretRef:
        name: astra-creds
  url: https://broker.astra.datastax.com/
```

## Contents of astra-service-instance.yaml

```js
apiVersion: servicecatalog.k8s.io/v1beta1
kind: ServiceInstance
metadata:
  name: devdb
  namespace: default
spec:
  parameters:
    capacity_units: 1
    cloud_provider: GCP
    keyspace: petclinic
    region: us-east1
  serviceClassExternalName: astra-database
  servicePlanExternalName: developer
```

## Contents of astra-service-binding.yaml

```js
apiVersion: servicecatalog.k8s.io/v1beta1
kind: ServiceBinding
metadata:
  name: devdb
spec:
  externalID: b946701e-c773-4332-8935-8212e8bdb412
  instanceRef:
    name: devdb
  secretName: devdb
```

# What's Next

Check out my next article in this series: [How To: Astra Multi Cloud](https://ds-steven-matison.github.io/astra/multi/cloud) where I show you how to use the concepts above to create a Production Cluster in Azure, AWS, and GCP!!

From the DevOps side this Astra Service Broker is just the tip of the iceberg.  Check out [Demo Spring Application](https://github.com/spring-petclinic/spring-petclinic-reactive/) which demonstrates how to role out an application using the database and security credentials exposed above.   If you are looking for more information about the Astra Service Broker check out the documentation [here](https://docs.astra.datastax.com/docs/astra-service-broker).

{% include astra_help.html %}