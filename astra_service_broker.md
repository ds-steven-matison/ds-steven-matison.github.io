---
layout: default
title: Astra Service Broker
description: How use Astra Service Broker
permalink: /astra/service/broker/
---

# How To: Astra Service Broker

Following Christopher Bradfords blog post over at Datastax: [Announcing the Astra Service Broker: Tradeoff-Free Cassandra in Kubernetes](https://www.datastax.com/blog/2020/11/announcing-astra-service-broker-tradeoff-free-cassandra-kubernetes) I was able to create a database on Astra in 8 commands using kubernetes and service account json object for my astra account. I had some challenges along the way but learned some great stuff today.  Check out the final command flow and the 2 source documents below.

```js
brew install k3d
k3d cluster create
helm install catalog svc-cat/catalog --namespace catalog --create-namespace
kubectl create secret generic astra-creds --from-literal=username=unused --from-literal=password=`echo '[Astra Service Account Credential JSON]'| base64`
svcat register astra --url https://broker.astra.datastax.com/ --basic-secret astra-creds
kubectl apply -f astra.yaml
kubectl get serviceinstances devdb
kubectl apply -f astra-service-binding.yaml
```

## Contents of astra.yaml

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