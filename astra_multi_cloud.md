---
layout: default
title: Astra Multi Cloud
description: How to deploy Astra Multi Cloud
permalink: /astra/multi/cloud/
---

# How To: Astra Multi Cloud
In the previous article [How To: Astra Service Broker](/astra/service/broker/) I showed you how to setup your environment and spin up a devdb in Astra free tier using the Astra Kubernetes Service Broker.  In this next how to I am going to show you how to spin up 3 production C10 instances in Astra.  One on [AWS](https://aws.amazon.com/), one on [Azure](https://azure.microsoft.com/), and one on [GCP](https://console.cloud.google.com).  Contents of the yamls are below, or check out my [github](https://github.com/ds-steven-matison/astra-service-broker-demo).

## Create the Instances
```js
kubectl apply -f astra-service-instance.yaml

serviceinstance.servicecatalog.k8s.io/devdb-gcp created
serviceinstance.servicecatalog.k8s.io/devdb-aws created
serviceinstance.servicecatalog.k8s.io/devdb-azure created
```
![WARNING](/assets/images/warning.png){:height="50" width="50" align="absmiddle" style="padding: 10px"} Magic is happening here, be patient.  My times were in excess of 20 minutes "provisioning".

![Astra Multi Cloud](/assets/images/astra-multi-cloud-provisioning.png)

## Check Instance Status

```js
kubectl get serviceinstances

NAME          CLASS                         PLAN   STATUS         AGE
devdb-azure   ServiceClass/astra-database   C10    Provisioning   40m
devdb-gcp     ServiceClass/astra-database   C10    Ready          40m
devdb-aws     ServiceClass/astra-database   C10    Ready          40m
```

At 40 minutes I can see all 3 Databases Active in Astra:
![Astra Multi Cloud](/assets/images/astra-multi-cloud-deployed.png)

## Bind the Instances

```js
kubectl apply -f astra-service-binding.yaml

servicebinding.servicecatalog.k8s.io/devdb-gcp created
servicebinding.servicecatalog.k8s.io/devdb-aws created
servicebinding.servicecatalog.k8s.io/devdb-azure created
```

## Check Binding Status

```js
kubectl get servicebindings                    

NAME          SERVICE-INSTANCE   SECRET-NAME   STATUS                  AGE
devdb-gcp     devdb-gcp          devdb-gcp     ErrorInstanceNotReady   9m44s
devdb-aws     devdb-aws          devdb-aws     ErrorInstanceNotReady   9m44s
devdb-azure   devdb-azure        devdb-azure   ErrorInstanceNotReady   9m44s
```
![WARNING](/assets/images/warning.png){:height="50" width="50" align="absmiddle" style="padding: 10px"} Again be patient here, rolling out all 3 cloud clusters is about 40 minutes.


## Get Instance Access Credentials

```js
kubectl get secrets devdb-gcp -o yaml
kubectl get secrets devdb-aws -o yaml
kubectl get secrets devdb-azure -o yaml
```

## Astra Service Instance YAML

```js
apiVersion: servicecatalog.k8s.io/v1beta1
kind: ServiceInstance
metadata:
  name: devdb-gcp
  namespace: default
spec:
  parameters:
    capacity_units: 1
    cloud_provider: GCP
    keyspace: petclinic
    region: us-east1
  serviceClassExternalName: astra-database
  servicePlanExternalName: C10
---
apiVersion: servicecatalog.k8s.io/v1beta1
kind: ServiceInstance
metadata:
  name: devdb-aws
  namespace: default
spec:
  parameters:
    capacity_units: 1
    cloud_provider: AWS
    keyspace: petclinic
    region: us-east-1
  serviceClassExternalName: astra-database
  servicePlanExternalName: C10
---
apiVersion: servicecatalog.k8s.io/v1beta1
kind: ServiceInstance
metadata:
  name: devdb-azure
  namespace: default
spec:
  parameters:
    capacity_units: 1
    cloud_provider: AZURE
    keyspace: petclinic
    region: eastus
  serviceClassExternalName: astra-database
  servicePlanExternalName: C10

```

## Astra Service Binding YAML

```js
apiVersion: servicecatalog.k8s.io/v1beta1
kind: ServiceBinding
metadata:
  name: devdb-gcp
spec:
  externalID: b946701e-c773-4332-8935-8212e8bdb412
  instanceRef:
    name: devdb-gcp
  secretName: devdb-gcp
---
apiVersion: servicecatalog.k8s.io/v1beta1
kind: ServiceBinding
metadata:
  name: devdb-aws
spec:
  externalID: b946701e-c773-4332-8935-8212e8bdb412
  instanceRef:
    name: devdb-aws
  secretName: devdb-gcp
---
apiVersion: servicecatalog.k8s.io/v1beta1
kind: ServiceBinding
metadata:
  name: devdb-azure
spec:
  externalID: b946701e-c773-4332-8935-8212e8bdb412
  instanceRef:
    name: devdb-azure
  secretName: devdb-gcp
```
## What's Next?

Check out some more [kubectl](/kubernetes/kubectl/) commands that you need to learn for managing your Astra instances.

{% include astra_help.html %}